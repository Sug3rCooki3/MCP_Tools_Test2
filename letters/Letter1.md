Subject: Finance Chatbot MVP — Build Plan and Specification Requirements

Dear Coding Agent,

Act as lead implementation planner and engineering architect for this project. Your job is to produce the planning documents, specs, sprint plans, and implementation foundation — then build it.

---

## 1. Product Definition

**What:** A single-user finance chatbot where the user enters transactions via chat, asks natural-language questions about their finances, and receives deterministic answers with chart-ready visualizations.

**Core user workflow:**
1. User enters transactions through the chat (e.g., "I spent $50 on groceries today"). GPT parses the intent and calls the Transaction CRUD tool to store the data.
2. User asks questions like "What did I spend on food in March?" or "Am I over budget this month?"
3. The system routes the question through GPT for intent parsing, calls MCP tools for the actual math, and returns a precise answer with optional chart data.

**Key design choice:** There is no form-based UI for data entry. All interaction — entering transactions, querying data, managing budgets — happens through the chat interface. GPT is responsible for parsing user input into structured tool calls.

**Key constraint:** GPT handles intent understanding, conversation, and explanation only. All financial math and data operations are handled by backend MCP tools. The language model must never perform calculations directly.

---

## 2. Technology Stack

Use this stack. Deviate only with explicit justification.

| Layer | Technology |
|---|---|
| Frontend | Next.js (TypeScript) |
| Backend / API / MCP server | Node.js (TypeScript) |
| Language | TypeScript across the full stack |
| Database | SQLite (single-user MVP; no multi-user concurrency needed) |
| Validation | Zod or similar schema validation on all tool inputs and outputs |
| Testing | Unit, integration, and E2E coverage |

---

## 3. Specification System

Create a `docs/specs/` folder containing one spec file per feature area. These specs are the source of truth for all engineering work.

### Each spec must include:

- Feature name and purpose
- User stories
- Functional requirements
- Non-functional requirements
- Acceptance criteria
- Data model requirements
- API / MCP tool requirements
- UI requirements (where relevant)
- Validation rules
- Error handling rules
- Security considerations
- Dependencies on other specs
- Test requirements (positive, negative, and edge cases at unit, integration, and E2E levels)

### Each spec must have a sprint plan with:

- Sprint goal
- Implementation tasks
- Expected deliverables
- Dependencies and risk areas
- Test tasks
- Done criteria

---

## 4. MVP Feature Areas

Each of these gets its own spec and sprint plan:

### 4.1 Project Foundation
- Repository structure, environment setup, configuration
- Coding standards, linting, formatting
- Local development workflow

### 4.2 Data Model and Storage
- **Transactions:** id, amount, category, date, description, created_at
- **Categories:** id, name (auto-created from transaction input when a new category name appears; no separate category management UI)
- **Budgets:** id, category, amount, period (monthly)
- Migration strategy for SQLite schema changes

### 4.3 Chat Interface
- Single chat interface for all user interaction (transaction entry, queries, budget management)
- Text input/output with message history
- Display of tool invocation results (structured data, not raw JSON)
- Confirmation prompts when GPT parses ambiguous transaction input (e.g., "Did you mean $50 in the Groceries category?")
- Loading and error states

### 4.4 GPT Orchestration Layer
- Request construction and tool-calling flow
- Structured output parsing
- Ambiguity detection: when GPT cannot confidently parse user input into a tool call, return a confirmation request to the chat interface instead of guessing
- Conversation context management (per-session, not persisted across sessions for MVP)
- Fallback behavior when GPT fails or returns unexpected output

### 4.5 MCP Tool Layer
All tools must have strict Zod schemas for inputs and outputs.

Required tools:
- **Transaction CRUD** — add, list, update, delete transactions (all invoked by GPT based on natural-language user input)
- **Budget CRUD** — create, update, delete monthly budgets per category (also chat-driven)
- **Spending summary** — totals and breakdowns by category or date range
- **Budget comparison** — actual spend vs. budget per category
- **Averages and percentages** — average spend per category, percentage of total
- **Chart data generation** — return data formatted for frontend chart rendering (not images)

### 4.6 Financial Calculation Engine
- Deterministic math layer used by MCP tools (no floating-point rounding surprises — use integer cents or a decimal library)
- Totals, category breakdowns, date-range filtering, averages, percentage calculations, budget vs. actual comparisons

### 4.7 Visualization Support
- Chart-ready JSON data contracts (e.g., labels + values arrays)
- Monthly and category-level views
- Frontend renders charts from structured data (use a library like Recharts or Chart.js)

### 4.8 Testing Strategy
This spec defines the testing framework, tooling, patterns, and coverage approach. Individual test cases are defined within each feature spec (4.1–4.7, 4.9), not here.

- Unit tests: isolated business logic, calculation engine, validation
- Integration tests: API handlers, database access, MCP tool execution, GPT orchestration boundaries
- E2E tests: full user flow from chat input through tool execution to rendered result
- Seed data fixtures for repeatable test runs
- Coverage target: aim for meaningful coverage, not a vanity percentage

### 4.9 CLI Export Utility
- A script that concatenates all source and test files into one output file
- Each file section must include the relative file path as a heading
- Must exclude `node_modules`, build output, and generated files
- Must be runnable with a single command (e.g., `npm run export`)

---

## 5. Scope Boundaries

**In scope for MVP:**
- Single user, no authentication
- Chat-based transaction entry only (no CSV import, no bank connections)
- Monthly budgets by category
- Basic chat with GPT + MCP tool flow
- One chart type per data view (bar or pie)
- SQLite local storage

**Out of scope for MVP (future work):**
- Multi-user support and authentication
- Bank account aggregation or CSV import
- Forecasting and predictive analytics
- Real-time notifications or alerts
- Mobile-specific UI
- CI/CD pipeline (document the plan, do not implement for MVP)

---

## 6. Engineering Principles

Apply pragmatically — do not over-engineer:

- **Separation of concerns:** clear boundaries between UI, orchestration, business logic, persistence, and tool execution
- **SOLID principles** where they reduce complexity, not where they add abstraction for its own sake
- **Explicit contracts:** typed interfaces between all layers
- **Deterministic financial logic:** no GPT-generated math, no floating-point money
- **Strong validation:** validate at system boundaries (API inputs, tool inputs/outputs, database writes)

---

## 7. Security and Data Handling

- Sanitize all user input before database writes
- Defend against prompt injection: validate and constrain GPT tool-call outputs before executing them — never trust GPT output as safe input to tools or database operations
- Do not log or expose raw financial data in error messages
- Do not send full transaction history to GPT — send only what is needed for the current query
- Store the GPT API key in environment variables, never in code

---

## 8. Expected Deliverables

1. Proposed repository structure
2. Complete specs in `docs/specs/` (one per feature area above)
3. Sprint plan per spec
4. Architecture diagram or description for the GPT → orchestration → MCP tool → database flow
5. Testing strategy embedded in every spec
6. CLI export script
7. Clear implementation order (which specs to build first)

---

## 9. Execution Order

Work in this sequence:

1. **Architecture and repo structure** — define before writing any code
2. **Data model and storage** — the foundation that everything else depends on
3. **Financial calculation engine** — core deterministic logic
4. **MCP tool layer** — expose calculations as callable tools
5. **GPT orchestration** — wire up intent parsing and tool calling
6. **Chat interface** — connect frontend to backend
7. **Visualization** — add chart data and rendering
8. **CLI export** — final utility

**Testing is not a separate step.** Write tests alongside each step above (steps 2–8). Do not defer testing to the end.

Produce actionable specs, not summaries. Another engineer should be able to implement directly from your output.