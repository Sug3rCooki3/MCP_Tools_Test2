# Sprint 8: CLI Export Utility and Final Integration

**References:** Letter1 Section 4.9

**Depends on:** All prior sprints (1–7)

---

## Sprint Goal

Build the CLI export utility and perform final integration validation. After this sprint, the full MVP is complete: all features work end-to-end, the codebase can be exported for review, and the project is ready for demonstration.

---

## Implementation Tasks

1. **Build the CLI export script**
   - Create a script (`scripts/export.ts`) that concatenates all source and test files into a single output file
   - Each file section must include the relative file path as a heading (e.g., `=== src/server/tools/transaction.ts ===`)
   - Output to a file (e.g., `export/codebase-export.txt`)

2. **Define file inclusion rules**
   - Include: all `.ts`, `.tsx`, `.json` (config files), `.md` (docs) files from `frontend/`, `server/`, `shared/`, `tests/`, `docs/`, and root config files
   - Exclude: `node_modules/`, build output (`.next/`, `dist/`), `.env`, database files (`.sqlite`, `.db`), `export/` output itself
   - Document the inclusion/exclusion rules in the script or a comment at the top of the output

3. **Define file ordering**
   - Order files logically: config files first, then shared types, then backend (data model → calculation engine → MCP tools → orchestration), then frontend, then tests, then docs
   - Not alphabetical — ordered for a reviewer reading top to bottom

4. **Add an npm script**
   - Add `"export": "ts-node scripts/export.ts"` (or equivalent) to `package.json`
   - Runnable with `npm run export`
   - Document in README

5. **Final integration sweep**
   - Run the full E2E test suite and confirm all tests pass
   - Walk through the complete user workflow manually:
     - Enter a transaction via chat
     - Enter several more transactions across different categories and dates
     - Ask for a spending summary
     - Ask for a budget comparison
     - Ask for a bar chart of spending by category
     - Ask for a pie chart of monthly spending
     - Send an ambiguous message and respond to the clarification
   - Confirm no regressions from earlier sprints

6. **Documentation check**
   - README covers: project overview, setup, development scripts, environment variables, architecture summary
   - All sprint files in `docs/_spec/sprints/` are present
   - Export script documented in README

---

## Expected Deliverables

- CLI export script that produces a single reviewable output file
- `npm run export` command working
- Full E2E test suite passing
- Manual walkthrough completed with no issues
- README finalized

---

## Dependencies

- All prior sprints (1–7): the entire codebase must be complete

---

## Risk Areas

- File ordering: the ordering logic may need adjustment as the actual file structure takes shape. Define a reasonable ordering now and refine during implementation.
- Large export file: for MVP scope, the export should be manageable. If it becomes very large, consider adding a table of contents with line numbers at the top.
- Regressions: the final integration sweep may surface issues introduced in later sprints that weren't caught by earlier tests. Budget time for fixes.

---

## Test Tasks

### Unit tests:
- Export script includes files matching inclusion rules
- Export script excludes files matching exclusion rules
- Export script adds correct file path headings
- Export script produces a non-empty output file
- File ordering matches the defined logical order

### Integration tests:
- Run `npm run export` → output file created at expected path
- Output file contains all expected source and test files
- Output file does not contain excluded files (node_modules, .env, build output)
- Output file has correct headings separating each file

### E2E tests (final full-stack validation):
- Complete user workflow: enter transactions → query summaries → compare budgets → view charts → handle ambiguity → all responses correct
- All prior E2E tests from sprints 5–7 still pass (regression check)

---

## Done Criteria

- [ ] `npm run export` produces a complete, correctly ordered export file
- [ ] Export excludes all specified files and directories
- [ ] Export includes file path headings for every section
- [ ] Full E2E test suite passes
- [ ] Manual walkthrough of complete user workflow succeeds
- [ ] README is complete and accurate
- [ ] No regressions from prior sprints
