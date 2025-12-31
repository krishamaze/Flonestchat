
================================================================================
PRODUCTION-GRADE AI CHATBOT WITH MEM0 GRAPH MEMORY
Next.js + Gemini AI + Mem0 Platform
================================================================================

## EXECUTIVE SUMMARY

Build a ChatGPT/Claude-level conversational AI with:
- Graph-based memory for relationship tracking and temporal reasoning
- Entity-scoped memory for multi-user/multi-session context
- Gemini AI Studio API for LLM responses with streaming
- Next.js 15 App Router with TypeScript
- Production deployment on Vercel/GCP

## CORE ARCHITECTURE

### Tech Stack Justification

| Component | Technology | Reasoning |
|-----------|-----------|-----------|
| **Frontend** | Next.js 15 App Router | Server components, streaming, edge runtime |
| **UI Framework** | React 19 + shadcn/ui | Type-safe components, accessibility |
| **LLM Provider** | Gemini 2.0 Flash/Pro | Cost-effective, 1M context, multimodal |
| **Memory Layer** | Mem0 Platform (Graph) | Relationship tracking, temporal reasoning |
| **Database** | Supabase Postgres | Auth, RLS, vector search, real-time |
| **Deployment** | Vercel Edge + GCP Run | Low latency, global CDN, autoscaling |
| **State Management** | Zustand + React Query | Minimal, predictable, server-sync |

### System Design

```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚                      CLIENT (Next.js)                        â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚  Chat UI â†’ Message Stream â†’ Context Panel â†’ Memory Graph    â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                   â”‚
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚              API ROUTES (Edge Functions)                     â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚  /chat/stream    â”‚  /memory/search   â”‚  /memory/add         â”‚
â”‚  /chat/export    â”‚  /entities/get    â”‚  /graph/visualize    â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                   â”‚
        â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
        â”‚                     â”‚
â”Œâ”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”   â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚  Gemini API    â”‚   â”‚   Mem0 Platform   â”‚
â”‚  (Streaming)   â”‚   â”‚  (Graph Memory)   â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤   â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚ - 2.0 Flash    â”‚   â”‚ - Entity nodes    â”‚
â”‚ - Function     â”‚   â”‚ - Relationship    â”‚
â”‚   calling      â”‚   â”‚   edges           â”‚
â”‚ - Multimodal   â”‚   â”‚ - Temporal links  â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜   â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                             â”‚
                   â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”
                   â”‚  Supabase DB     â”‚
                   â”‚ - Conversations  â”‚
                   â”‚ - Messages       â”‚
                   â”‚ - User profiles  â”‚
                   â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

================================================================================
PHASE 1: FOUNDATION & MEMORY INTEGRATION
================================================================================

**Goal:** Set up project structure and integrate Mem0 graph memory

### Batch 1.1: Project Initialization (30 mins)

```bash
# Create Next.js project
npx create-next-app@latest ai-chatbot \
  --typescript --tailwind --app --src-dir

# Install core dependencies
cd ai-chatbot
npm install @ai-sdk/google ai \
  @supabase/supabase-js @supabase/ssr \
  zustand @tanstack/react-query \
  zod date-fns uuid

# Install UI dependencies
npm install @radix-ui/react-* \
  class-variance-authority clsx tailwind-merge \
  lucide-react

# Dev dependencies
npm install -D @types/uuid @types/node
```

**Environment Setup (.env.local):**
```env
# Gemini AI
GOOGLE_GENERATIVE_AI_API_KEY=your_key

# Mem0 Platform
MEM0_API_KEY=your_key
MEM0_PROJECT_ID=your_project_id

# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_key
SUPABASE_SERVICE_ROLE_KEY=your_key
```

**Action Items:**
1. Create project with TypeScript strict mode
2. Configure shadcn/ui with custom theme
3. Set up environment variables
4. Create folder structure: /lib, /components, /app/api

**Deliverable:** Running Next.js app with dependencies installed

---

### Batch 1.2: Mem0 Client Setup (45 mins)

**File: `lib/mem0/client.ts`**

```typescript
import axios from 'axios';

interface Mem0Config {
  apiKey: string;
  projectId: string;
  baseUrl?: string;
}

interface Memory {
  id: string;
  memory: string;
  user_id?: string;
  agent_id?: string;
  metadata?: Record<string, any>;
  created_at: string;
  updated_at: string;
}

interface AddMemoryParams {
  messages: Array<{ role: 'user' | 'assistant'; content: string }>;
  user_id?: string;
  agent_id?: string;
  metadata?: Record<string, any>;
  custom_categories?: string[];
  infer?: boolean; // Enable graph memory extraction
}

interface SearchMemoryParams {
  query: string;
  user_id?: string;
  agent_id?: string;
  limit?: number;
  filters?: Record<string, any>;
}

class Mem0Client {
  private apiKey: string;
  private projectId: string;
  private baseUrl: string;
  private headers: Record<string, string>;

  constructor(config: Mem0Config) {
    this.apiKey = config.apiKey;
    this.projectId = config.projectId;
    this.baseUrl = config.baseUrl || 'https://api.mem0.ai/v1';
    this.headers = {
      'Authorization': `Token ${this.apiKey}`,
      'Content-Type': 'application/json',
    };
  }

  async addMemory(params: AddMemoryParams): Promise<Memory[]> {
    const response = await axios.post(
      `${this.baseUrl}/memories/`,
      {
        ...params,
        project_id: this.projectId,
      },
      { headers: this.headers }
    );
    return response.data.results;
  }

  async searchMemory(params: SearchMemoryParams): Promise<Memory[]> {
    const response = await axios.post(
      `${this.baseUrl}/memories/search/`,
      {
        ...params,
        project_id: this.projectId,
      },
      { headers: this.headers }
    );
    return response.data.results;
  }

  async getMemories(userId: string, limit = 50): Promise<Memory[]> {
    const response = await axios.get(
      `${this.baseUrl}/memories/`,
      {
        headers: this.headers,
        params: { user_id: userId, limit, project_id: this.projectId },
      }
    );
    return response.data.results;
  }

  async deleteMemory(memoryId: string): Promise<void> {
    await axios.delete(
      `${this.baseUrl}/memories/${memoryId}/`,
      { headers: this.headers }
    );
  }

  async updateMemory(memoryId: string, data: Partial<Memory>): Promise<Memory> {
    const response = await axios.put(
      `${this.baseUrl}/memories/${memoryId}/`,
      data,
      { headers: this.headers }
    );
    return response.data;
  }

  // Graph memory specific: Get entity relationships
  async getEntityGraph(userId: string): Promise<any> {
    const response = await axios.get(
      `${this.baseUrl}/entities/`,
      {
        headers: this.headers,
        params: { user_id: userId, project_id: this.projectId },
      }
    );
    return response.data;
  }
}

// Singleton instance
let mem0Client: Mem0Client | null = null;

export function getMem0Client(): Mem0Client {
  if (!mem0Client) {
    if (!process.env.MEM0_API_KEY || !process.env.MEM0_PROJECT_ID) {
      throw new Error('Mem0 credentials not configured');
    }

    mem0Client = new Mem0Client({
      apiKey: process.env.MEM0_API_KEY,
      projectId: process.env.MEM0_PROJECT_ID,
    });
  }
  return mem0Client;
}

export type { Memory, AddMemoryParams, SearchMemoryParams };
```

**Action Items:**
1. Create Mem0 client wrapper with TypeScript types
2. Implement core methods: add, search, get, delete, update
3. Add graph-specific entity retrieval
4. Test with curl/Postman against Mem0 API

**Validation:**
```bash
# Test Mem0 connection
curl -X POST https://api.mem0.ai/v1/memories/ \
  -H "Authorization: Token YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"messages":[{"role":"user","content":"Test"}],"user_id":"test"}'
```

**Deliverable:** Working Mem0 client with graph memory support

---

### Batch 1.3: Supabase Schema (30 mins)

**File: `supabase/migrations/001_init.sql`**

```sql
-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Users table (synced with Supabase Auth)
CREATE TABLE users (
  id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  email TEXT UNIQUE NOT NULL,
  full_name TEXT,
  avatar_url TEXT,
  mem0_user_id TEXT UNIQUE, -- Maps to Mem0 entity
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Conversations table
CREATE TABLE conversations (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  title TEXT,
  model TEXT DEFAULT 'gemini-2.0-flash',
  system_prompt TEXT,
  metadata JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Messages table
CREATE TABLE messages (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  conversation_id UUID REFERENCES conversations(id) ON DELETE CASCADE,
  role TEXT NOT NULL CHECK (role IN ('user', 'assistant', 'system')),
  content TEXT NOT NULL,
  mem0_memory_ids TEXT[], -- Track which memories were created
  token_count INTEGER,
  metadata JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Memory snapshots (cache of Mem0 data for offline access)
CREATE TABLE memory_snapshots (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  mem0_memory_id TEXT UNIQUE NOT NULL,
  content TEXT NOT NULL,
  category TEXT,
  entities JSONB DEFAULT '[]', -- Graph entities extracted
  synced_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes for performance
CREATE INDEX idx_conversations_user_id ON conversations(user_id);
CREATE INDEX idx_messages_conversation_id ON messages(conversation_id);
CREATE INDEX idx_messages_created_at ON messages(created_at DESC);
CREATE INDEX idx_memory_snapshots_user_id ON memory_snapshots(user_id);

-- Row Level Security
ALTER TABLE conversations ENABLE ROW LEVEL SECURITY;
ALTER TABLE messages ENABLE ROW LEVEL SECURITY;
ALTER TABLE memory_snapshots ENABLE ROW LEVEL SECURITY;

-- Policies
CREATE POLICY "Users can view own conversations"
  ON conversations FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create own conversations"
  ON conversations FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can view own messages"
  ON messages FOR SELECT
  USING (
    EXISTS (
      SELECT 1 FROM conversations
      WHERE conversations.id = messages.conversation_id
      AND conversations.user_id = auth.uid()
    )
  );

CREATE POLICY "Users can create own messages"
  ON messages FOR INSERT
  WITH CHECK (
    EXISTS (
      SELECT 1 FROM conversations
      WHERE conversations.id = messages.conversation_id
      AND conversations.user_id = auth.uid()
    )
  );
```

**Action Items:**
1. Run migration in Supabase dashboard
2. Verify RLS policies work
3. Create initial user via Supabase Auth
4. Test CRUD operations

**Deliverable:** Database schema with RLS enabled

---

================================================================================
PHASE 2: GEMINI STREAMING & MEMORY PIPELINE
================================================================================

**Goal:** Implement AI chat with real-time streaming and memory integration

### Batch 2.1: Gemini Streaming Route (1 hour)

**File: `app/api/chat/route.ts`**

```typescript
import { google } from '@ai-sdk/google';
import { streamText, tool } from 'ai';
import { z } from 'zod';
import { getMem0Client } from '@/lib/mem0/client';
import { createClient } from '@/lib/supabase/server';

export const runtime = 'edge';
export const maxDuration = 60;

export async function POST(req: Request) {
  try {
    const { messages, conversationId, userId } = await req.json();

    // Get Supabase client
    const supabase = createClient();

    // Get Mem0 client
    const mem0 = getMem0Client();

    // Search relevant memories
    const lastUserMessage = messages.filter((m: any) => m.role === 'user').slice(-1)[0];
    const relevantMemories = await mem0.searchMemory({
      query: lastUserMessage.content,
      user_id: userId,
      limit: 5,
    });

    // Build context from memories
    const memoryContext = relevantMemories.length > 0
      ? `\n\nRelevant memories about the user:\n${relevantMemories
          .map((m) => `- ${m.memory}`)
          .join('\n')}`
      : '';

    // Add system context
    const systemMessage = {
      role: 'system',
      content: `You are a helpful AI assistant with long-term memory. 
You remember past conversations and user preferences.${memoryContext}

Always provide personalized, context-aware responses based on what you know about the user.`,
    };

    // Stream response from Gemini
    const result = await streamText({
      model: google('gemini-2.0-flash-exp'),
      messages: [systemMessage, ...messages],
      temperature: 0.7,
      maxTokens: 2048,
      tools: {
        searchMemory: tool({
          description: 'Search through past conversations and memories',
          parameters: z.object({
            query: z.string().describe('Search query'),
          }),
          execute: async ({ query }) => {
            const results = await mem0.searchMemory({
              query,
              user_id: userId,
              limit: 3,
            });
            return { memories: results.map((m) => m.memory) };
          },
        }),
      },
      onFinish: async ({ text, usage }) => {
        // Save assistant message
        await supabase.from('messages').insert({
          conversation_id: conversationId,
          role: 'assistant',
          content: text,
          token_count: usage.totalTokens,
        });

        // Add conversation to Mem0 (background job)
        if (messages.length >= 2) {
          const lastPair = messages.slice(-2);
          await mem0.addMemory({
            messages: lastPair,
            user_id: userId,
            metadata: { conversation_id: conversationId },
            infer: true, // Enable graph extraction
          });
        }
      },
    });

    return result.toDataStreamResponse();
  } catch (error) {
    console.error('Chat error:', error);
    return new Response('Internal server error', { status: 500 });
  }
}
```

**Action Items:**
1. Implement streaming endpoint with Vercel AI SDK
2. Integrate Mem0 memory search for context
3. Add tool calling for dynamic memory retrieval
4. Save messages to Supabase after completion

**Deliverable:** Working streaming chat with memory-augmented responses

---

### Batch 2.2: Chat UI Component (1 hour)

**File: `components/chat/ChatInterface.tsx`**

```typescript
'use client';

import { useChat } from 'ai/react';
import { useEffect, useRef } from 'react';
import { Send, Loader2 } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Textarea } from '@/components/ui/textarea';
import { MessageBubble } from './MessageBubble';
import { MemoryPanel } from './MemoryPanel';

interface ChatInterfaceProps {
  conversationId: string;
  userId: string;
  initialMessages?: any[];
}

export function ChatInterface({
  conversationId,
  userId,
  initialMessages = [],
}: ChatInterfaceProps) {
  const {
    messages,
    input,
    setInput,
    isLoading,
    append,
    reload,
  } = useChat({
    api: '/api/chat',
    initialMessages,
    body: { conversationId, userId },
  });

  const messagesEndRef = useRef<HTMLDivElement>(null);

  // Auto-scroll
  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages]);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    if (!input.trim() || isLoading) return;

    await append({
      role: 'user',
      content: input,
    });
    setInput('');
  };

  return (
    <div className="flex h-screen">
      {/* Main chat area */}
      <div className="flex-1 flex flex-col">
        {/* Messages */}
        <div className="flex-1 overflow-y-auto p-4 space-y-4">
          {messages.map((message) => (
            <MessageBubble
              key={message.id}
              message={message}
              isLoading={isLoading && message.role === 'assistant'}
            />
          ))}
          <div ref={messagesEndRef} />
        </div>

        {/* Input */}
        <form onSubmit={handleSubmit} className="p-4 border-t">
          <div className="flex gap-2">
            <Textarea
              value={input}
              onChange={(e) => setInput(e.target.value)}
              placeholder="Message AI..."
              className="resize-none"
              rows={3}
              onKeyDown={(e) => {
                if (e.key === 'Enter' && !e.shiftKey) {
                  e.preventDefault();
                  handleSubmit(e);
                }
              }}
            />
            <Button type="submit" disabled={isLoading || !input.trim()}>
              {isLoading ? (
                <Loader2 className="h-4 w-4 animate-spin" />
              ) : (
                <Send className="h-4 w-4" />
              )}
            </Button>
          </div>
        </form>
      </div>

      {/* Memory sidebar */}
      <MemoryPanel userId={userId} conversationId={conversationId} />
    </div>
  );
}
```

**Action Items:**
1. Build responsive chat UI with shadcn components
2. Implement auto-scrolling and keyboard shortcuts
3. Add loading states and error handling
4. Create memory sidebar for context visualization

**Deliverable:** Functional chat interface with streaming support

---

================================================================================
PHASE 3: ADVANCED MEMORY FEATURES
================================================================================

**Goal:** Implement graph visualization, memory filters, and contextual understanding

### Batch 3.1: Memory Management API (45 mins)

**File: `app/api/memory/route.ts`**

```typescript
import { getMem0Client } from '@/lib/mem0/client';
import { NextRequest } from 'next/server';

export async function GET(req: NextRequest) {
  const searchParams = req.nextUrl.searchParams;
  const userId = searchParams.get('userId');
  const query = searchParams.get('query');

  if (!userId) {
    return Response.json({ error: 'User ID required' }, { status: 400 });
  }

  const mem0 = getMem0Client();

  try {
    if (query) {
      const results = await mem0.searchMemory({ query, user_id: userId });
      return Response.json({ memories: results });
    }

    const memories = await mem0.getMemories(userId);
    return Response.json({ memories });
  } catch (error) {
    return Response.json({ error: 'Failed to fetch me
