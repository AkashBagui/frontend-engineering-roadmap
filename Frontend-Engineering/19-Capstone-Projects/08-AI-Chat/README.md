# AI Chat Application

## Project Overview

Build an AI chat application with streaming responses, conversation history, markdown rendering, code syntax highlighting, file uploads, and support for multiple AI providers (OpenAI, Anthropic, Google). This project introduces the Vercel AI SDK for streaming, prompt engineering patterns, and real-time UI updates.

## Learning Objectives

- Vercel AI SDK for streaming responses (useChat, useCompletion)
- Server-Sent Events (SSE) for real-time streaming
- Multiple AI provider integration (OpenAI, Anthropic, Google)
- Markdown rendering with rehype/rehype plugins
- Code syntax highlighting with Shiki or Prism
- Conversation management (history, branching)
- File upload and processing (PDF, images for vision)
- Prompt engineering and system prompts
- Rate limiting and token management
- Abort controller and cancellation patterns

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| Next.js 14 | Framework | App Router, Edge runtime, API routes |
| TypeScript | Language | Type safety |
| Vercel AI SDK | AI streaming | useChat, useCompletion, streaming protocol |
| Tailwind CSS | Styling | Utility classes, dark mode |
| Prisma + Postgres | Database | Conversation storage |
| Auth.js | Authentication | User sessions |
| Shiki | Syntax highlighting | VS Code-quality highlighting |
| react-markdown | Markdown | Render AI markdown responses |
| Zustand | State management | Chat UI state |
| TanStack Query | Server state | Conversation history |

## Feature List

### MVP Features
- Chat interface with streaming responses
- Multiple AI provider support (OpenAI GPT-4, Anthropic Claude)
- Conversation history (create, rename, delete)
- Markdown rendering (headings, lists, tables, links)
- Code syntax highlighting with copy button
- Dark/light theme
- Message editing and regeneration
- Abort/stop generation
- Token count and usage display
- Responsive design

### Advanced Features
- File upload (PDF, TXT, images) with vision support
- Conversation branching (fork from any message)
- System prompt customization
- Temperature and model parameter controls
- Prompt templates and presets
- Search across conversations
- Export conversation (markdown, PDF, JSON)
- Voice input (speech-to-text)
- Image generation (DALL-E, Stable Diffusion)
- Web search augmentation (RAG)
- Tool/function calling integration

## Architecture Diagram

```
src/
├── app/
│   ├── layout.tsx
│   ├── page.tsx                    # Redirect to chat
│   ├── (auth)/
│   │   ├── login/
│   │   │   └── page.tsx
│   │   └── register/
│   │       └── page.tsx
│   ├── (chat)/
│   │   ├── layout.tsx              # Sidebar + chat area
│   │   ├── page.tsx                # Empty chat (new conversation)
│   │   └── [id]/
│   │       └── page.tsx            # Existing conversation
│   └── api/
│       ├── chat/
│       │   └── route.ts            # Streaming chat endpoint
│       ├── conversations/
│       │   └── route.ts            # CRUD conversations
│       └── upload/
│           └── route.ts            # File upload
├── components/
│   ├── layout/
│   │   ├── Sidebar.tsx
│   │   ├── Header.tsx
│   │   └── ThemeProvider.tsx
│   ├── chat/
│   │   ├── ChatInterface.tsx       # Main chat wrapper
│   │   ├── ChatMessages.tsx        # Message list
│   │   ├── ChatMessage.tsx         # Single message (user/assistant)
│   │   ├── ChatInput.tsx           # Input with send, upload, options
│   │   ├── StreamingMessage.tsx    # Real-time streaming message
│   │   ├── WelcomeScreen.tsx       # Empty state with suggestions
│   │   ├── StopButton.tsx
│   │   ├── RegenerateButton.tsx
│   │   └── ModelSelector.tsx       # Provider/model picker
│   ├── markdown/
│   │   ├── MarkdownRenderer.tsx    # react-markdown wrapper
│   │   ├── CodeBlock.tsx           # Shiki syntax highlight
│   │   ├── TableRenderer.tsx
│   │   └── ImageRenderer.tsx
│   ├── conversations/
│   │   ├── ConversationList.tsx
│   │   ├── ConversationItem.tsx
│   │   ├── SearchConversations.tsx
│   │   └── ConversationSettings.tsx
│   └── ui/
│       ├── Button.tsx
│       ├── Input.tsx
│       ├── Select.tsx
│       ├── Dropdown.tsx
│       ├── Avatar.tsx
│       └── Toast.tsx
├── stores/
│   ├── useChatStore.ts             # UI state (sidebar, settings)
│   └── useSettingsStore.ts         # Model prefs, theme
├── hooks/
│   ├── useChat.ts                  # Wraps Vercel AI SDK useChat
│   ├── useConversations.ts
│   └── useFileUpload.ts
├── lib/
│   ├── ai/
│   │   ├── providers.ts            # OpenAI, Anthropic, Google config
│   │   ├── prompts.ts              # System prompts, templates
│   │   └── streaming.ts           # Stream configuration
│   ├── prisma.ts
│   ├── auth.ts
│   └── utils.ts
├── types/
│   ├── chat.ts
│   └── conversation.ts
└── styles/
    └── globals.css
```

## Component Tree

```
<ChatLayout>
  <Sidebar>
    <NewChatButton />
    <SearchConversations />
    <ConversationList>
      <ConversationItem />*         {/* Title, date, delete */}
    </ConversationList>
    <BottomSection>
      <ModelSelector />             {/* Provider + model */}
      <SettingsButton />
      <UserMenu />
    </BottomSection>
  </Sidebar>
  <ChatArea>
    <Header>
      <ConversationTitle />         {/* Editable */}
      <ShareButton />
      <ExportButton />
    </Header>
    <ChatMessages>                  {/* Scrollable */}
      <WelcomeScreen />             {/* When no messages */}
      <ChatMessage type="user">     {/* User message */}
        <MarkdownRenderer />
      </ChatMessage>
      <ChatMessage type="assistant">
        <StreamingMessage>          {/* During generation */}
          <MarkdownRenderer />
          <CodeBlock />*            {/* For code snippets */}
        </StreamingMessage>
        <MessageActions>
          <CopyButton />
          <RegenerateButton />
          <EditButton />
        </MessageActions>
      </ChatMessage>
      {/* ... more messages */}
    </ChatMessages>
    <ChatInput>
      <FileUploadButton />
      <TextArea />                  {/* Auto-resize */}
      <SendButton />
      <StopButton />                {/* During streaming */}
      <ModelIndicator />            {/* Current model */}
    </ChatInput>
  </ChatArea>
</ChatLayout>
```

## Data Flow

### Chat Streaming Flow
```
User types message → ChatInput → useChat.sendMessage()
                                       ↓
                        POST /api/chat (stream: true)
                                       ↓
                        Server: AI SDK streamText()
                        → OpenAI/Anthropic stream response
                                       ↓
                        Response: ReadableStream (SSE)
                                       ↓
                        Client: useChat reads chunks
                        → Updates message content in real-time
                                       ↓
                        On complete: save conversation to DB
                                       ↓
                        Update conversation list (last message preview)
```

### Conversation Data Flow
```
Load conversation → /api/conversations/[id]
                          ↓
              TanStack Query fetch
                          ↓
              useChat.setMessages(messages)
                          ↓
              Render message list with MarkdownRenderer
                          ↓
              User sends new message → streaming continues
```

## Route Structure

| Route | Type | Auth | Description |
|-------|------|------|-------------|
| `/` | Server | Required | Redirect to /chat |
| `/chat` | Client | Required | New conversation |
| `/chat/[id]` | Client | Required | Existing conversation |
| `/login` | Client | — | Login page |
| `/register` | Client | — | Register page |
| `/api/chat` | Edge | Required | Streaming chat endpoint |
| `/api/conversations` | API | Required | Conversation CRUD |
| `/api/conversations/[id]` | API | Required | Single conversation |
| `/api/upload` | API | Required | File upload handler |

## Database Schema

```prisma
model User {
  id             String         @id @default(cuid())
  email          String         @unique
  name           String?
  conversations  Conversation[]
  settings       UserSettings?
  createdAt      DateTime       @default(now())
}

model UserSettings {
  id            String @id @default(cuid())
  userId        String @unique
  user          User   @relation(fields: [userId], references: [id])
  theme         String @default("system")
  defaultModel  String @default("gpt-4o")
  systemPrompt  String?
  temperature   Float  @default(0.7)
}

model Conversation {
  id         String      @id @default(cuid())
  title      String      @default("New conversation")
  model      String      @default("gpt-4o")
  userId     String
  user       User        @relation(fields: [userId], references: [id])
  messages   Message[]
  createdAt  DateTime    @default(now())
  updatedAt  DateTime    @updatedAt
}

model Message {
  id             String       @id @default(cuid())
  conversationId String
  conversation   Conversation @relation(fields: [conversationId], references: [id])
  role           String       // "user" | "assistant" | "system" | "tool"
  content        String       // Markdown content
  tokens         Int?         // Token count for this message
  metadata       Json?        // Provider, model, latency
  createdAt      DateTime     @default(now())
}

model FileUpload {
  id        String   @id @default(cuid())
  name      String
  type      String   // "pdf", "txt", "image"
  url       String
  size      Int
  messageId String?
  message   Message? @relation(fields: [messageId], references: [id])
  createdAt DateTime @default(now())
}
```

## API Route Design

```typescript
// /api/chat/route.ts
import { openai } from '@ai-sdk/openai';
import { anthropic } from '@ai-sdk/anthropic';
import { google } from '@ai-sdk/google';
import { streamText, ToolSet } from 'ai';

export async function POST(req: Request) {
  const { messages, model, provider } = await req.json();

  const modelMap = {
    openai: openai('gpt-4o'),
    anthropic: anthropic('claude-3-opus'),
    google: google('gemini-pro'),
  };

  const result = streamText({
    model: modelMap[provider as keyof typeof modelMap],
    messages,
    temperature: 0.7,
    maxTokens: 4096,
  });

  return result.toAIStreamResponse();
}
```

## Key Implementation Considerations

- Use `useChat` from Vercel AI SDK for streaming — handles reconnection, abort, error states
- Implement abort controller to stop generation mid-stream
- Use Edge Runtime for API routes (lower latency, global distribution)
- Store messages in DB after streaming completes (not during — avoid partial saves)
- Implement conversation title auto-generation (AI suggests title after 2nd message)
- Use react-markdown with rehype-highlight or Shiki for code blocks
- Implement file upload with multipart/form-data, store in S3/Cloudinary
- Support for vision models — send image URL/file in message content
- Implement rate limiting per user (Vercel KV or in-memory with sliding window)

## Performance Considerations

- Use Edge Runtime for chat API (lower latency than Node.js)
- Stream response in chunks — UI updates progressively
- Virtualize conversation list for 100+ conversations
- Lazy load code highlighting (Shiki worker — heavy bundle)
- Debounce conversation title auto-save
- Use `next/dynamic` for markdown renderer (heavy dependency)
- Implement message pagination (load recent 50, load more on scroll up)
- Offload heavy computation (PDF parsing) to server-side

## Deployment Strategy

1. **Vercel** — Edge Runtime for chat API, Serverless for REST
2. **Neon.tech** for Postgres database
3. **Cloudinary or S3** for file uploads
4. **Upstash Redis** for rate limiting and token tracking
5. **Environment variables**: API keys (OpenAI, Anthropic, Google), database URL
6. **CI/CD**: GitHub Actions → lint → test → build → deploy
7. **Monitoring**: Vercel Analytics + Sentry for error tracking

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Architecture, provider setup, data model | 1 |
| Foundation | Next.js setup, auth, layouts, sidebar | 1.5 |
| Chat Streaming | Vercel AI SDK integration, streaming UI | 2 |
| Markdown | react-markdown, syntax highlighting, copy | 1.5 |
| Conversations | CRUD, list, search, auto-title | 1.5 |
| Multi-Provider | OpenAI, Anthropic, Google — switchable | 1 |
| File Upload | Upload, parse, attach to messages | 1.5 |
| Polish | Abort, regenerate, edit, error handling | 1.5 |
| Performance | Edge runtime, lazy loading, optimization | 1 |
| Deploy | Environment, CI/CD, monitoring | 0.5 |
| **Total** | | **~8-12 days** |

## Learning Resources

- [Vercel AI SDK Documentation](https://sdk.vercel.ai/docs)
- [OpenAI API Reference](https://platform.openai.com/docs/api-reference)
- [Anthropic Claude API](https://docs.anthropic.com/en/docs)
- [react-markdown](https://github.com/remarkjs/react-markdown)
- [Shiki Syntax Highlighting](https://shiki.style/)
- [Edge Runtime](https://vercel.com/docs/functions/edge-functions/edge-runtime)
- [Server-Sent Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)
