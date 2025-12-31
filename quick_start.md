
================================================================================
QUICK START GUIDE - AI CHATBOT WITH MEM0 GRAPH MEMORY
================================================================================

## IMMEDIATE NEXT STEPS (Start Here)

### 1. Create Mem0 Account & Enable Graph Memory
```bash
# Visit https://app.mem0.ai
# Create account â†’ New Project â†’ Enable "Graph Memory"
# Copy: API Key + Project ID
```

**Critical:** During project setup, toggle "Graph Memory" ON. This enables:
- Entity extraction (people, places, concepts)
- Relationship mapping (knows, works_at, likes)
- Temporal tracking (before, after, during)

### 2. Get Gemini API Key
```bash
# Visit https://aistudio.google.com/app/apikey
# Create API key
# Important: Use gemini-2.0-flash-exp for cost efficiency
```

### 3. Initialize Project (5 mins)
```bash
npx create-next-app@latest ai-chatbot --typescript --tailwind --app --src-dir
cd ai-chatbot

# Install critical dependencies
npm install @ai-sdk/google ai zod
npm install axios  # For Mem0 API
npm install @supabase/supabase-js @supabase/ssr

# Create .env.local
cat > .env.local << EOL
GOOGLE_GENERATIVE_AI_API_KEY=your_gemini_key
MEM0_API_KEY=your_mem0_key
MEM0_PROJECT_ID=your_project_id
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
EOL
```

================================================================================
ARCHITECTURE DECISIONS (Why These Choices)
================================================================================

### Why Mem0 Graph Memory over Vector-Only?

**Vector Memory (Base Mem0):**
- Fast: 0.7s median latency
- Good for: Simple recall, FAQ-style queries
- Limitation: Can't track "John introduced me to Sarah in 2024"

**Graph Memory (Mem0^g):**
- Slower: ~1.2s median latency (still acceptable)
- Excellent for: Multi-hop reasoning, temporal relationships
- Example: "Who did John introduce me to?" â†’ Traverses graph: User â†’ knows â†’ John â†’ introduced â†’ Sarah

**Decision:** Use Graph for ChatGPT-level context understanding.

### Why Gemini 2.0 Flash over GPT-4?

| Feature | Gemini 2.0 Flash | GPT-4 Turbo |
|---------|------------------|-------------|
| Input cost | $0.075/1M tokens | $10/1M tokens |
| Output cost | $0.30/1M tokens | $30/1M tokens |
| Context window | 1M tokens | 128K tokens |
| Latency | ~800ms | ~1200ms |
| **Cost ratio** | **133x cheaper** | Baseline |

**Decision:** Gemini Flash for 90% of conversations, upgrade to Pro for complex tasks.

### Why Next.js Edge Runtime?

- **Traditional Node.js:** 200-500ms cold start
- **Edge Runtime:** 0-50ms cold start, global distribution
- **Streaming:** Native support for SSE, critical for chat UX

**Decision:** Deploy chat routes to Edge, use Node.js for background jobs.

================================================================================
CRITICAL IMPLEMENTATION DETAILS
================================================================================

## Mem0 Graph Memory Integration

### Enable Graph Extraction (MUST DO)
```typescript
// When adding memories, set infer: true
await mem0.addMemory({
  messages: [
    { role: 'user', content: 'I met John at Google last week' },
    { role: 'assistant', content: 'Nice! How was meeting John?' }
  ],
  user_id: 'user_123',
  infer: true,  // â† THIS ENABLES GRAPH EXTRACTION
});

// Mem0 will automatically extract:
// - Entity: John (Person)
// - Entity: Google (Organization)
// - Relationship: user_123 â†’ met â†’ John
// - Relationship: John â†’ works_at â†’ Google
// - Temporal: last_week
```

### Context-Aware Memory Search
```typescript
// Before generating response, search relevant memories
const memories = await mem0.searchMemory({
  query: userMessage,
  user_id: userId,
  limit: 5,
  filters: {
    // Optional: Filter by entity type
    'metadata.entity_type': 'person'
  }
});

// Inject into system prompt
const systemPrompt = `You are an AI assistant with memory.

Relevant context from past conversations:
${memories.map(m => `- ${m.memory}`).join('\n')}

Use this context to provide personalized responses.`;
```

## Gemini Streaming Pattern

```typescript
import { google } from '@ai-sdk/google';
import { streamText } from 'ai';

export async function POST(req: Request) {
  const { messages, userId } = await req.json();

  // Fetch relevant memories
  const memories = await mem0.searchMemory({
    query: messages[messages.length - 1].content,
    user_id: userId,
  });

  // Stream with memory context
  const result = await streamText({
    model: google('gemini-2.0-flash-exp'),
    messages: [
      {
        role: 'system',
        content: `Context: ${memories.map(m => m.memory).join('; ')}`
      },
      ...messages
    ],
    temperature: 0.7,
    onFinish: async ({ text }) => {
      // Add conversation to memory (runs async)
      await mem0.addMemory({
        messages: messages.slice(-2),
        user_id: userId,
        infer: true,
      });
    }
  });

  return result.toDataStreamResponse();
}
```

================================================================================
COMMON PITFALLS & SOLUTIONS
================================================================================

### Pitfall 1: Memory Overload
**Problem:** Injecting 50+ memories into context â†’ high token cost
**Solution:** 
```typescript
// Limit to top 5 most relevant
const memories = await mem0.searchMemory({
  query: userMessage,
  user_id: userId,
  limit: 5,  // â† Critical
});
```

### Pitfall 2: Stale Memory Cache
**Problem:** User updates preference, but old memory still used
**Solution:**
```typescript
// Use Mem0's built-in deduplication
await mem0.addMemory({
  messages: [...],
  user_id: userId,
  infer: true,
  custom_categories: ['preferences'], // Groups similar memories
});

// Mem0 automatically merges/updates conflicting memories
```

### Pitfall 3: Graph Memory Not Working
**Problem:** Entities not extracted even with infer: true
**Solution:**
```typescript
// Ensure conversation has enough context
const messages = [
  { role: 'user', content: 'I work at Google' },  // Too vague
  { role: 'assistant', content: 'Cool!' }
];

// Better:
const messages = [
  { 
    role: 'user', 
    content: 'I just started working at Google as a software engineer in Mountain View' 
  },  // Explicit entities
  { role: 'assistant', content: 'Congrats on the new role!' }
];
```

### Pitfall 4: Rate Limiting
**Problem:** 429 errors from Mem0 during high traffic
**Solution:**
```typescript
// Implement exponential backoff
async function addMemoryWithRetry(params: AddMemoryParams, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      return await mem0.addMemory(params);
    } catch (error) {
      if (error.response?.status === 429) {
        await new Promise(resolve => setTimeout(resolve, 2 ** i * 1000));
        continue;
      }
      throw error;
    }
  }
}
```

================================================================================
TESTING CHECKLIST
================================================================================

## Phase 1: Basic Functionality
â–¡ Chat message sends successfully
â–¡ Gemini streams response in real-time
â–¡ Response includes markdown formatting
â–¡ Loading states display correctly

## Phase 2: Memory Integration
â–¡ After 2-message exchange, memory is created in Mem0
â–¡ Verify in Mem0 dashboard: https://app.mem0.ai
â–¡ Next message retrieves relevant memory
â–¡ System prompt includes memory context

## Phase 3: Graph Verification
â–¡ Send message: "I work at Google with Sarah"
â–¡ Check Mem0 entities tab â†’ Should see:
  - Entity: Google (Organization)
  - Entity: Sarah (Person)
  - Relationship: user â†’ works_at â†’ Google
  - Relationship: user â†’ works_with â†’ Sarah

â–¡ Send follow-up: "Tell me about my colleagues"
â–¡ Response should mention Sarah specifically

## Phase 4: Edge Cases
â–¡ Empty message â†’ Shows validation error
â–¡ Network error â†’ Displays retry option
â–¡ Long response (>2000 tokens) â†’ Streams smoothly
â–¡ Concurrent requests â†’ No race conditions

================================================================================
PERFORMANCE BENCHMARKS (Target)
================================================================================

| Metric | Target | Measurement |
|--------|--------|-------------|
| Time to First Token | < 800ms | From submit to first stream chunk |
| Memory Search Latency | < 500ms | Mem0 API call duration |
| Full Response Time | < 3s | For 500-token response |
| UI Frame Rate | 60 FPS | During streaming |
| Bundle Size | < 200KB | Initial JS load |

================================================================================
DEPLOYMENT SEQUENCE
================================================================================

### Pre-Deployment
1. Run `npm run build` locally â†’ Verify no errors
2. Test in production mode: `npm start`
3. Load test with 10 concurrent users
4. Check bundle analysis: `npm run build -- --analyze`

### Deploy to Vercel
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Set environment variables
vercel env add GOOGLE_GENERATIVE_AI_API_KEY production
vercel env add MEM0_API_KEY production
vercel env add MEM0_PROJECT_ID production

# Promote to production
vercel --prod
```

### Post-Deployment
1. Test production URL
2. Monitor Vercel Analytics for errors
3. Check Mem0 dashboard for memory creation
4. Verify Supabase logs for database writes

================================================================================
MONITORING SETUP (Week 1)
================================================================================

### Key Metrics to Track

1. **Mem0 Memory Operations**
   - Dashboard: https://app.mem0.ai/analytics
   - Watch: Daily memory creation rate, search latency

2. **Gemini API Usage**
   - Dashboard: https://aistudio.google.com/app/quota
   - Watch: Token consumption, rate limit hits

3. **Vercel Performance**
   - Dashboard: https://vercel.com/analytics
   - Watch: Edge function duration, error rate

4. **User Behavior**
   - Track: Average messages per session
   - Track: Memory retrieval accuracy (manual review)

================================================================================
NEXT PHASE IDEAS (After MVP)
================================================================================

1. **Multi-Modal Support**
   - Add image understanding with Gemini Vision
   - Store visual memories (Mem0 supports multimodal)

2. **Voice Interface**
   - Integrate ElevenLabs for voice responses
   - Use Gemini's audio input (when available)

3. **Memory Analytics**
   - Build dashboard showing memory graph
   - Visualize entity relationships over time

4. **Collaborative Memory**
   - Shared conversations between users
   - Team-level memory pools

5. **Memory Export**
   - Let users download their memory graph
   - GDPR compliance features

================================================================================
SUPPORT RESOURCES
================================================================================

Mem0 Docs: https://docs.mem0.ai/platform/overview
Mem0 Discord: https://discord.gg/mem0ai
Gemini API Docs: https://ai.google.dev/docs
Vercel AI SDK: https://sdk.vercel.ai/docs
Next.js 15 Docs: https://nextjs.org/docs

================================================================================
