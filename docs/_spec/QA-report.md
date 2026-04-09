# QA Report — All Sprints

**Date:** 2026-04-09  
**Checklist used:** [QA-checklist.md](QA-checklist.md)  
**Files checked:** 8 WebSprints + 5 graphSprints (13 total)

---

## Summary

| Sprint | Status | Issues Found |
|---|---|---|
| WebSprint 1 — Foundation | ✅ Pass | 0 (previously QA'd) |
| WebSprint 2 — Data Model | ✅ Pass | 0 |
| WebSprint 3 — Calculation Engine | ⚠️ Fix | 2 unresolved decisions |
| WebSprint 4 — MCP Tools | ⚠️ Fix | 1 overlap with graph sprints |
| WebSprint 5 — GPT Orchestration | ⚠️ Fix | 1 unresolved decision |
| WebSprint 6 — Chat Interface | ✅ Pass | 0 |
| WebSprint 7 — Visualization | ⚠️ Fix | 2 (overlap + unresolved decision) |
| WebSprint 8 — CLI Export | ⚠️ Fix | 2 (wrong directory + unresolved decision) |
| Graph Sprint 1 — Schema Validation | ⚠️ Fix | 1 incorrect dependency |
| Graph Sprint 2 — Aggregation | ✅ Pass | 0 |
| Graph Sprint 3 — GPT Integration | ✅ Pass | 0 |
| Graph Sprint 4 — Frontend Rendering | ⚠️ Fix | 1 overlap note needed |
| Graph Sprint 5 — Final Integration | ✅ Pass | 0 |

**Total issues: 10** (2 major, 5 medium, 3 minor)

---

## Major Issues

### 1. Sprint 4 / Graph Sprints Overlap (Rule 7: Logic — no contradictions)

**File:** `WebSprints/sprint-4-mcp-tools.md`

Sprint 4 Task 7 implements `get_spending_chart_data` fully, but Graph Sprints 1–2 also implement it from scratch (stub handler → real aggregation). This creates duplicate work and confusion about which sprint to follow.

**Fix:** Sprint 4 defers the chart tool to the Graph Sprint series. Task 7 becomes a forward reference. Tool count drops from 7 to 6.

---

### 2. Sprint 7 / Graph Sprint 4 Overlap (Rule 7: Logic — no contradictions)

**File:** `WebSprints/sprint-7-visualization.md` and `graphSprints/graph-sprint-4-frontend-rendering.md`

Sprint 7 and Graph Sprint 4 implement nearly identical work: chart library, chart component, inline rendering, empty state, highlights, error handling. Both files would confuse an engineer about which to follow.

**Fix:** Sprint 7 becomes a high-level reference pointing to the Graph Sprint series for detailed implementation. Graph Sprint 4 remains the detailed source of truth.

---

## Medium Issues

### 3. Sprint 3 Task 6 — Percentage format undecided (Rule 10: Clarity)

**File:** `WebSprints/sprint-3-calculation-engine.md`

Says: "Return as integer basis points (e.g., 2550 = 25.50%) or as a decimal with defined precision"

Two options without a decision. Inconsistent with the integer-cents principle used everywhere else.

**Fix:** Decide on integer basis points (10000 = 100.00%) to stay consistent with the integer-for-all-money principle.

---

### 4. Sprint 3 Task 8 — Schema format undecided (Rule 10: Clarity)

**File:** `WebSprints/sprint-3-calculation-engine.md`

Says: "Use Zod schemas or TypeScript interfaces (consistent with the rest of the codebase)"

The project uses Zod everywhere. No reason to leave this open.

**Fix:** Use Zod schemas for validation, TypeScript interfaces for internal typing. Both, with Zod as the source of truth.

---

### 5. Sprint 5 Task 2 — Tool sync strategy undecided (Rule 10: Clarity)

**File:** `WebSprints/sprint-5-gpt-orchestration.md`

Says: "single source of truth or generated from Zod schemas"

Two options without a decision.

**Fix:** Generate GPT tool definitions from the Zod schemas (single source of truth). This avoids drift between the schemas and GPT's view of the tools.

---

### 6. Sprint 7 Task 1 / Graph Sprint 4 Task 1 — Chart library undecided (Rule 10: Clarity)

**File:** `WebSprints/sprint-7-visualization.md` and `graphSprints/graph-sprint-4-frontend-rendering.md`

Both say "Choose Recharts or Chart.js." An engineer shouldn't have to make this decision mid-sprint.

**Fix:** Recommend Recharts (native React components, no wrapper needed, better Next.js integration).

---

### 7. Sprint 8 Task 1 — Script language undecided (Rule 10: Clarity)

**File:** `WebSprints/sprint-8-cli-export.md`

Says: "scripts/export.ts or scripts/export.sh"

**Fix:** Use `scripts/export.ts` for TypeScript consistency across the stack.

---

## Minor Issues

### 8. Graph Sprint 1 — Incorrect dependency in header (Rule 6: Dependencies)

**File:** `graphSprints/graph-sprint-1-schema-validation.md`

The "Depends on" header lists "Letter1 Sprint 3 (calculation engine)" but the stub handler doesn't use the calculation engine. The detailed Dependencies section correctly omits Sprint 3. The header is wrong.

**Fix:** Remove Sprint 3 from the "Depends on" header.

---

### 9. Sprint 4 — Tool count says 7 (Rule 7: Logic)

**File:** `WebSprints/sprint-4-mcp-tools.md`

Deliverables and Done Criteria say "7 tools" but after deferring the chart tool to graph sprints, the count is 6.

**Fix:** Update to "6 tools" and note the chart tool is deferred to the Graph Sprint series.

---

### 10. Sprint 8 — Wrong directory name and missing `shared/` (Rule 4: Consistency with Letter1)

**File:** `WebSprints/sprint-8-cli-export.md`

Task 2 says `src/` but Sprint 1 uses `frontend/`. Also doesn't include the `shared/` directory defined in Sprint 1.

**Fix:** Change `src/` to `frontend/` and add `shared/`.

---

## Passing Sprints — Notes

### WebSprint 1 (Foundation)
Previously QA'd. All 10 checklist categories pass.

### WebSprint 2 (Data Model)
All checks pass. Schema matches Letter1 Section 4.2. Integer cents enforced. Auto-creation of categories present. Input sanitization covered. Comprehensive tests.

### WebSprint 6 (Chat Interface)
All checks pass. Chat-only design consistent. No form references. Loading/error states covered. First E2E tests defined.

### Graph Sprint 2 (Aggregation)
All checks pass. Uses calculation engine correctly. Date range/category filtering present. Sort orders explicitly defined. Empty results handled.

### Graph Sprint 3 (GPT Integration)
All checks pass. Ambiguity detection present. Data minimization enforced (summary, not raw arrays). Parameter validation before execution.

### Graph Sprint 5 (Final Integration)
All checks pass. All 10 Letter2 acceptance criteria listed as checkboxes. Determinism verification. Security tests. Regression testing against Letter1 features.
