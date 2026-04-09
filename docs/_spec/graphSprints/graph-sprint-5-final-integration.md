# Graph Sprint 5: Final Integration and Testing

**References:** Letter2 Section 9 (acceptance criteria), Letter2 Section 7 (testing requirements)

**Depends on:** Graph Sprints 1–4

---

## Sprint Goal

Validate the complete graph feature end-to-end, close any gaps, and confirm all acceptance criteria from Letter2 are met. After this sprint, the spending graph feature is fully implemented, tested, and ready for MVP.

---

## Implementation Tasks

1. **Run the full test suite**
   - Execute all unit, integration, and E2E tests from Graph Sprints 1–4
   - Fix any failures or regressions

2. **Manual walkthrough of all graph scenarios**
   - Pie chart of spending by category for the current year
   - Bar chart of monthly spending for the current year
   - Chart with category filter (e.g., "Show me a pie chart of food and rent spending")
   - Chart with a specific date range
   - Chart with a specific year
   - Ambiguous request → clarification → correct chart
   - Request with no matching data → empty state
   - Invalid request (e.g., impossible date range) → safe error
   - Switch from bar to pie in follow-up message

3. **Verify acceptance criteria from Letter2 Section 9**
   - [ ] 1. User can request a spending graph through the chat
   - [ ] 2. Backend returns correct, deterministic chart-ready JSON
   - [ ] 3. Frontend renders a bar or pie chart from the tool output
   - [ ] 4. Category and date-range filtering work correctly via chat
   - [ ] 5. GPT explains the graph result using tool data
   - [ ] 6. Empty data produces a valid empty state
   - [ ] 7. Invalid inputs are rejected with safe error responses
   - [ ] 8. Prompt injection defenses are in place
   - [ ] 9. Unit, integration, and E2E test coverage exists
   - [ ] 10. Feature spec is documented in `docs/specs/`

4. **Verify determinism**
   - Run the same request multiple times against the same seeded data
   - Confirm the output is identical every time (same labels order, same values, same highlights)

5. **Verify prompt injection defenses**
   - Attempt to pass SQL injection via category names
   - Attempt to pass unexpected chartType values
   - Attempt to pass script tags or command strings in parameters
   - Confirm all are rejected by Zod validation before reaching the database or calculation engine

6. **Write the feature spec in `docs/specs/`**
   - Create a spec file following the Letter1 Section 3 template
   - Include: feature name, user stories, functional requirements, acceptance criteria, test requirements
   - Reference this graph sprint series for the implementation plan

7. **Check for regressions in Letter1 features**
   - Run the full Letter1 E2E test suite
   - Confirm transaction CRUD, spending summaries, budget comparisons, and other tools still work correctly alongside the new graph tool

---

## Expected Deliverables

- All graph feature tests passing
- Manual walkthrough completed with no issues
- All 10 acceptance criteria verified
- Determinism verified
- Prompt injection defenses verified
- Feature spec written in `docs/specs/`
- No regressions in existing features

---

## Dependencies

- Graph Sprints 1–4: complete implementation
- Letter1 Sprints 1–8: existing features for regression testing

---

## Risk Areas

- Regressions: adding the graph tool to the GPT tool definitions might affect tool selection for non-graph requests. Verify that existing tools are still selected correctly.
- Edge interactions: a user might ask for a summary and a chart in the same message (e.g., "How much did I spend this year and show me a chart"). The orchestration layer handles one tool call at a time for MVP — document this as a known limitation if it surfaces.

---

## Test Tasks

### Regression tests:
- All Letter1 E2E tests pass (transaction CRUD, summaries, budget comparisons)
- GPT still selects non-graph tools correctly for non-graph requests

### Determinism tests:
- Same input + same data → identical output across 5 consecutive runs

### Security tests:
- SQL injection in `includeCategories` → rejected
- Invalid enum value in `chartType` → rejected
- Script injection in parameters → rejected
- All rejected at validation, before any database access

### Full E2E acceptance tests:
- Pie chart by category → renders correctly with GPT explanation
- Bar chart by month → renders correctly with GPT explanation
- Category filter → chart shows only filtered categories
- Date range filter → chart shows only matching transactions
- Year filter → chart scoped to that year
- Ambiguous request → clarification → correct chart
- Empty data → empty state message
- Invalid input → safe error message
- Bar to pie switch → new chart renders
- All responses include GPT explanation text

---

## Done Criteria

- [ ] All unit, integration, and E2E tests pass
- [ ] Manual walkthrough completed — all scenarios work
- [ ] All 10 Letter2 acceptance criteria verified
- [ ] Determinism confirmed across repeated runs
- [ ] Prompt injection defenses verified
- [ ] Feature spec written in `docs/specs/`
- [ ] No regressions in Letter1 features
- [ ] Graph feature is complete and ready for MVP
