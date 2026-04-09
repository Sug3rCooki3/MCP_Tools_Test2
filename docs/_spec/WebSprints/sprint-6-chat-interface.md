# Sprint 6: Chat Interface

**References:** Letter1 Sections 4.3, 1 (design choice)

**Depends on:** Sprint 5 (GPT orchestration layer)

---

## Sprint Goal

Build the Next.js chat interface that serves as the single point of interaction for the user. After this sprint, the user can enter transactions, ask questions, manage budgets, and receive structured responses — all through chat. This is where the full stack connects end-to-end for the first time.

---

## Implementation Tasks

1. **Build the chat message input**
   - Text input field with send button
   - Submit on Enter key press
   - Disable input while a request is in progress
   - Clear input after sending

2. **Build the message history display**
   - Scrollable message list showing user and assistant messages
   - Auto-scroll to the latest message
   - Messages persist in the UI for the current session (not across sessions)
   - Each message rendered with sender identification (user vs. assistant)

3. **Connect to the backend API**
   - Send user messages to a Next.js API route (e.g., `POST /api/chat`)
   - API route calls the GPT orchestration layer (Sprint 5)
   - Return the assistant's response to the frontend
   - Handle request/response lifecycle cleanly

4. **Render tool invocation results**
   - When the backend returns structured data (e.g., spending summary, budget comparison), render it in a readable format — not raw JSON
   - Use simple formatted displays: tables, labeled values, key-value pairs
   - Chart data rendering is handled in Sprint 7 — for now, display chart data as a structured summary

5. **Implement confirmation prompts**
   - When the backend returns a clarification request (from GPT ambiguity detection, Sprint 5), display it as a follow-up question in the chat
   - The user responds in the chat, and the conversation continues naturally

6. **Implement loading state**
   - Show a loading indicator while waiting for the backend response
   - Prevent duplicate submissions during loading

7. **Implement error states**
   - Display user-friendly error messages when the backend returns an error
   - Handle network failures (backend unreachable)
   - Never expose raw error details, stack traces, or internal data to the user

8. **Basic styling and layout**
   - Clean, minimal chat layout — centered conversation area, input at the bottom
   - Responsive enough to be usable on desktop (mobile-specific UI is out of scope)
   - No design system needed — keep it simple for MVP

---

## Expected Deliverables

- Chat input with send functionality
- Message history display with user/assistant messages
- Backend API route connected to GPT orchestration
- Structured rendering of tool results (non-chart)
- Clarification prompt display
- Loading and error states
- Basic responsive layout

---

## Dependencies

- Sprint 5: GPT orchestration layer (full backend pipeline)
- Sprint 1: Next.js frontend scaffolding

---

## Risk Areas

- Streaming vs. blocking: for MVP, a blocking request/response is simpler. Streaming (SSE/WebSocket) can be added later if response times feel slow.
- Tool result rendering: the format of tool results will vary per tool. Define a simple rendering strategy (e.g., a switch on result type) rather than building a complex component system.
- Session state: conversation history lives in React state for MVP. No persistence, no localStorage. If the user refreshes, the session resets.

---

## Test Tasks

### Unit tests:
- Message input component: sends message on Enter, clears after send, disables during loading
- Message list component: renders user and assistant messages correctly
- Loading indicator: shows during pending request, hides after response
- Error display: renders user-friendly error for various error types

### Integration tests:
- Send a message → backend receives it → response displayed in chat
- Structured tool result → rendered as formatted data (not raw JSON)
- Clarification request from backend → displayed as follow-up question
- Backend error → user-friendly error message displayed

### E2E tests (first full-stack E2E tests):
- User types "I spent $50 on groceries today" → transaction is stored → confirmation message displayed
- User types "What did I spend on food this month?" → spending summary displayed with correct amounts
- User types "Set my food budget to $200" → budget created → confirmation displayed
- User types "Am I over budget on food?" → budget comparison displayed
- User sends ambiguous input → clarification prompt displayed → user responds → correct result displayed
- User sends message while backend is down → error message displayed

---

## Done Criteria

- [ ] User can send messages and see responses in the chat
- [ ] Conversation history displays correctly for the session
- [ ] Tool invocation results render as structured data (not raw JSON)
- [ ] Clarification prompts work end-to-end
- [ ] Loading state prevents duplicate submissions
- [ ] Error states display user-friendly messages
- [ ] First full-stack E2E tests pass
- [ ] Layout is clean and usable on desktop
