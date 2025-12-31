# NeuroChat - Mobile-First AI Chat with Graph Memory

NeuroChat is a production-grade, mobile-first AI chatbot UX built with Next.js and a long-term memory layer backed by Mem0 graph memory.
The UI is designed for thumb-driven navigation, fast streaming responses, and modern mobile interaction patterns (drawers, sheets, gestures).

## Mobile-first UX principles

- Thumb-zone first: Primary actions stay in the bottom area (Send, Attach, Voice, Menu).
- One-hand friendly navigation: Use bottom sheets on mobile, not sidebars.
- Keyboard-safe layout: Input stays visible with the on-screen keyboard; no layout jumping.
- Touch targets: Minimum 44px hit targets, generous spacing, and safe-area padding.
- Streaming-first: Response streams token-by-token with smooth scroll anchoring.
- Offline-tolerant: Cache conversation list and last messages for fast re-entry.

## Advanced UI features

- Adaptive layout:
  - Mobile: Bottom sheet for Memory and Settings.
  - Tablet/Desktop: Collapsible sidebar + split pane.
- Message rendering:
  - Markdown with code blocks.
  - Mobile-friendly horizontal code scrolling (no sideways page scroll).
- Interaction patterns:
  - Swipe actions on conversation list (archive, delete).
  - Long-press on messages (copy, quote, pin).
  - Animated transitions (spring-style) for sheets and route changes.
- Theming:
  - Light/Dark with system sync.
  - True-black theme option for OLED devices.
  - High-contrast mode support.

## Tech stack

- Next.js (App Router) + TypeScript
- Tailwind CSS + shadcn/ui
- Streaming LLM: Gemini via Google AI Studio API key
- Memory: Mem0 (graph memory)
- Database/Auth: Supabase (Postgres, RLS)

## Setup

### Prerequisites

- Node.js 20+
- Google AI Studio API key
- Mem0 API key + project ID (graph memory enabled)
- Supabase project (optional but recommended for auth + persistence)

### Environment variables

Create `.env.local`:

```env
GOOGLE_GENERATIVE_AI_API_KEY=your_key

MEM0_API_KEY=your_key
MEM0_PROJECT_ID=your_project_id

NEXT_PUBLIC_SUPABASE_URL=your_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
