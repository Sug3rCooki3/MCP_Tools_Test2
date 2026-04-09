# QA Checklist for Sprint and Letter Review

This document defines the QA rules used to review all letters and sprint files in this project. Every file must pass all applicable checks before it is considered clean.

---

## 1. Grammar and Spelling

- [ ] No spelling errors
- [ ] No grammatical errors
- [ ] Consistent use of singular "the user" (not "users") — single-user MVP
- [ ] No missing words (e.g., "foundation everything depends on" → needs "that")
- [ ] No wording that contradicts design decisions (e.g., "manually" when the design is chat-based)

---

## 2. Markdown Formatting

- [ ] Valid Markdown throughout (headings, tables, lists, bold, horizontal rules)
- [ ] Tables have correct column alignment and separators
- [ ] Code references use backticks (e.g., `npm run dev`)
- [ ] Section headings are properly hierarchical (## → ### → ####)

---

## 3. Section Structure and Numbering

- [ ] All sections are numbered sequentially with no gaps
- [ ] Every sprint file contains: Sprint Goal, Implementation Tasks, Expected Deliverables, Dependencies, Risk Areas, Test Tasks, Done Criteria
- [ ] Done Criteria use checkboxes (`- [ ]`) for verifiable items

---

## 4. Consistency with Letter1

- [ ] No authentication references — MVP is single-user, no auth
- [ ] No form-based UI — all interaction is chat-driven
- [ ] Database is SQLite (not PostgreSQL or other)
- [ ] Validation library is Zod
- [ ] Tech stack matches: Next.js, Node.js, TypeScript
- [ ] Spec folder path is `docs/specs/` (not `docs/spec_folder`)
- [ ] All money values stored as integer cents (no floating-point)
- [ ] Prompt injection defense required for all tool inputs
- [ ] Data minimization: do not send full transaction history to GPT
- [ ] API key stored in environment variables, never in code
- [ ] Categories are auto-created from transaction input (no separate management UI)

---

## 5. Consistency with Letter2 (for graph sprints)

- [ ] Tool name is `get_spending_chart_data`
- [ ] Input schema matches Letter2 Section 3 (chartType, groupBy, startDate, endDate, year, includeCategories)
- [ ] Output schema matches Letter2 Section 3 (title, chartType, labels, values, currency, total, dateRange, appliedCategories, highlights)
- [ ] `highlights` field allows null for empty results
- [ ] Chart types are bar and pie only
- [ ] Empty results return valid empty output (not errors)

---

## 6. Dependency Chain

- [ ] Each sprint's "Depends on" section lists correct prior sprints
- [ ] No sprint depends on a later sprint (no circular dependencies)
- [ ] Dependencies align with Letter1 Section 9 execution order
- [ ] Graph sprints depend on the correct Letter1 sprints

---

## 7. Logic and Completeness

- [ ] Every implementation task has a corresponding deliverable
- [ ] Every deliverable has a corresponding done criterion
- [ ] No logic gaps: if Feature A requires Feature B, Feature B is either in this sprint or a dependency
- [ ] No contradictions between sections within the same file
- [ ] No contradictions between the sprint and its referenced letter sections
- [ ] Risk areas are realistic and actionable (not vague)
- [ ] Ambiguities are resolved with a decision or documented as a decision point

---

## 8. Test Coverage

- [ ] Test tasks cover unit, integration, and E2E levels (where applicable)
- [ ] Tests include positive, negative, and edge cases
- [ ] Tests are specific and verifiable (not vague like "test the feature")
- [ ] Empty/zero data cases are tested
- [ ] Invalid input cases are tested
- [ ] Tests align with the implementation tasks (nothing implemented without a test, no test for something not implemented)

---

## 9. Security

- [ ] Prompt injection defense mentioned for any sprint involving tool inputs
- [ ] Input sanitization mentioned for any sprint involving database writes
- [ ] No raw financial data in error messages or logs
- [ ] GPT API key handled via environment variables
- [ ] GPT tool-call outputs validated before execution

---

## 10. Clarity for Implementation

- [ ] An engineer could implement directly from this sprint without guessing
- [ ] Folder paths and file locations are explicit (not "e.g." without a decision)
- [ ] Technology choices are concrete (not "choose one of X or Y" without guidance)
- [ ] Sort orders, precedence rules, and default behaviors are defined
- [ ] Structural decisions are made (e.g., monorepo vs. folder-based, API routes vs. separate server)
