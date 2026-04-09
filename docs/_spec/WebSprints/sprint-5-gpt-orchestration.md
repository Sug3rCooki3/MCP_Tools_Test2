# Sprint 5: GPT Orchestration Layer

**References:** Letter1 Sections 4.4, 7

**Depends on:** Sprint 4 (MCP tools registered and callable)

---

## Sprint Goal

Build the GPT orchestration layer that connects user chat messages to MCP tool calls. After this sprint, the system can receive a natural-language message, route it through GPT, invoke the correct MCP tool, and return the tool's result — without GPT performing any calculations itself.

---

## Implementation Tasks

1. **Set up GPT API client**
   - Configure the OpenAI (or compatible) API client
   - Load API key from environment variables (never hardcode)
   - Set model, temperature, and token limits appropriate for tool-calling
   - Implement retry logic for transient API failures

2. **Define the tool-calling interface for GPT**
   - Register all MCP tools as GPT function/tool definitions
   - Include tool names, descriptions, and parameter schemas in the format GPT expects
   - Ensure tool definitions stay in sync with actual MCP tool schemas (single source of truth or generated from Zod schemas)

3. **Implement request construction**
   - Build the GPT request from: system prompt, conversation history, user message, and available tools
   - System prompt must instruct GPT to: parse intent, select tools, never perform calculations, and ask for clarification when input is ambiguous
   - Limit conversation history sent to GPT (per-session only, not full history)

4. **Implement tool-call parsing and execution**
   - Parse GPT's response to detect tool calls
   - Extract tool name and parameters from GPT's response
   - Validate extracted parameters against the tool's Zod schema before execution (prompt injection defense)
   - Execute the tool via the MCP layer
   - Return the tool's result to GPT for natural-language explanation

5. **Implement ambiguity detection flow**
   - When GPT indicates it cannot confidently parse the user's input, return a clarification request to the frontend instead of guessing
   - Define a structured format for clarification requests (e.g., `{ type: "clarification", message: "Did you mean..." }`)
   - The frontend displays this as a follow-up question in the chat

6. **Implement conversation context management**
   - Maintain per-session conversation history in memory
   - Include relevant prior messages in GPT requests for context
   - Do not persist conversation across sessions for MVP
   - Cap context window to avoid exceeding token limits — drop oldest messages first

7. **Implement fallback and error handling**
   - GPT API failure (timeout, rate limit, 500): return a user-friendly error message
   - GPT returns unexpected output (no tool call, malformed response): return a fallback message
   - Tool execution failure: return a user-friendly error without exposing internals
   - Log errors for debugging (without exposing raw financial data)

8. **Implement data minimization for GPT**
   - Do not send full transaction history to GPT
   - Send only the user's message, conversation context, and tool results
   - Tool results sent back to GPT for explanation should be summarized, not raw database dumps

---

## Expected Deliverables

- GPT API client configured and connected
- Tool definitions registered with GPT
- Request construction with system prompt, context, and tools
- Tool-call parsing, validation, and execution pipeline
- Ambiguity detection and clarification flow
- Per-session conversation context management
- Error handling and fallback behavior
- Data minimization enforced

---

## Dependencies

- Sprint 4: all MCP tools registered and callable
- Sprint 1: GPT API key in environment variables

---

## Risk Areas

- System prompt quality: the system prompt is critical for GPT behavior. It must clearly instruct GPT to never calculate, always use tools, and ask for clarification when unsure. Expect iteration.
- Token limits: conversation history can grow quickly. Implement truncation early, not after hitting limits.
- Tool selection accuracy: GPT may occasionally select the wrong tool or extract incorrect parameters. The validation layer (prompt injection defense) catches unsafe inputs, but incorrect-but-valid inputs may produce unexpected results. E2E tests will surface these.
- API cost: during development, use lower-cost models or mock the GPT API for unit/integration tests. Only use the production model for E2E tests.

---

## Test Tasks

### Unit tests:
- Request construction produces correctly formatted GPT API payloads
- Tool-call parser extracts tool name and parameters from GPT responses
- Tool-call parser handles GPT responses with no tool call (plain text response)
- Tool-call parser handles malformed GPT responses gracefully
- Conversation context truncation drops oldest messages first
- Parameter validation rejects invalid tool parameters before execution

### Integration tests:
- Full pipeline: user message → GPT API call → tool call detected → MCP tool executed → result returned
- GPT selects the correct tool for a transaction entry message (e.g., "I spent $30 on lunch")
- GPT selects the correct tool for a query message (e.g., "What did I spend on food this month?")
- GPT returns a clarification request for ambiguous input (e.g., "I spent some money")
- Invalid tool parameters from GPT are caught by validation and not executed
- GPT API failure returns a user-friendly error message
- Tool execution failure returns a user-friendly error message

### E2E tests (deferred to Sprint 6 when the chat interface exists):
- User sends a message → receives a response through the full stack

---

## Done Criteria

- [ ] GPT API client connects and returns responses
- [ ] All MCP tools registered as GPT tool definitions
- [ ] Tool calls are parsed, validated, and executed correctly
- [ ] Ambiguity detection returns clarification requests
- [ ] Conversation context managed per-session with truncation
- [ ] Fallback behavior handles all error cases
- [ ] Data minimization enforced — no raw transaction dumps sent to GPT
- [ ] All unit and integration tests pass
- [ ] System prompt documented and version-controlled
