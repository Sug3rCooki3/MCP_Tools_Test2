# Sprint 2: Data Model and Storage

**References:** Letter1 Sections 4.2, 7

**Depends on:** Sprint 1 (repo structure, SQLite driver, Zod)

---

## Sprint Goal

Define and implement the SQLite database schema for transactions, categories, and budgets. Create the data access layer with full CRUD operations, validation, and seed data. After this sprint, the database is ready to be used by the calculation engine and MCP tools.

---

## Implementation Tasks

1. **Define the database schema**
   - `transactions` table: id (primary key, auto-increment), amount (integer, stored in cents), category (text), date (text, ISO format), description (text), created_at (text, ISO timestamp)
   - `categories` table: id (primary key, auto-increment), name (text, unique)
   - `budgets` table: id (primary key, auto-increment), category (text), amount (integer, stored in cents), period (text, default "monthly")
   - Add indexes: `transactions.date`, `transactions.category`

2. **Create migration strategy**
   - Write an initial migration script that creates all three tables
   - Document how future schema changes should be handled (e.g., numbered migration files run in order)
   - Migration runs automatically on first startup if tables do not exist

3. **Implement data access functions**
   - **Transactions:** create, getById, list (with optional date-range and category filters), update, delete
   - **Categories:** create (auto-created when a new category appears in a transaction), list, getByName
   - **Budgets:** create, getByCategoryAndPeriod, list, update, delete

4. **Implement Zod schemas for all data**
   - Transaction input schema (validate amount > 0, valid date, non-empty description, non-empty category)
   - Budget input schema (validate amount > 0, non-empty category, valid period)
   - Shared output schemas for query results

5. **Implement auto-creation of categories**
   - When a transaction is created with a category name that doesn't exist, create the category automatically
   - Category names should be normalized (e.g., trimmed, case-insensitive matching)

6. **Create seed data utility**
   - A script or function that populates the database with sample transactions, categories, and budgets
   - Cover multiple categories, multiple months, and varying amounts
   - Used for development and testing

7. **Sanitize inputs**
   - All string inputs trimmed and sanitized before database writes (Letter1 Section 7)
   - Reject or escape any potentially harmful input

---

## Expected Deliverables

- SQLite schema with all three tables and indexes
- Migration script that runs on first startup
- Full CRUD data access layer for transactions, categories, and budgets
- Zod validation schemas for all inputs and outputs
- Category auto-creation logic
- Seed data script
- Input sanitization layer

---

## Dependencies

- Sprint 1: SQLite driver, Zod, project structure

---

## Risk Areas

- Integer cents: ensure amount is always stored and returned as an integer (cents). Never allow floating-point amounts into the database. Validate at the Zod schema level.
- Category normalization: decide early on a strategy (e.g., lowercase + trim) and apply it consistently in both creation and lookup.
- Date format: standardize on ISO 8601 (`YYYY-MM-DD`) and validate strictly.

---

## Test Tasks

### Unit tests:
- Zod schemas accept valid inputs and reject invalid inputs (wrong types, missing fields, negative amounts, empty strings, invalid dates)
- Category name normalization produces consistent results
- Amount validation rejects zero, negative, and non-integer values

### Integration tests:
- Create a transaction → verify it exists in the database with correct fields
- Create a transaction with a new category → verify category is auto-created
- Create a transaction with an existing category → verify no duplicate category
- List transactions with date-range filter → returns only matching records
- List transactions with category filter → returns only matching records
- Update a transaction → verify changes are persisted
- Delete a transaction → verify it no longer exists
- Create, list, update, delete budgets → full lifecycle
- Seed data script populates the database with expected records

### Edge case tests:
- Create a transaction with maximum reasonable amount
- Create a transaction with a very long description
- Filter transactions with a date range that matches no records → returns empty array
- Delete a transaction that doesn't exist → returns appropriate response
- Create a budget for a category that has no transactions → succeeds

---

## Done Criteria

- [ ] All three tables created with correct schemas and indexes
- [ ] Migration runs automatically on first startup
- [ ] Transaction CRUD works end-to-end
- [ ] Category auto-creation works
- [ ] Budget CRUD works end-to-end
- [ ] All Zod schemas validate correctly
- [ ] Seed data script populates a usable development database
- [ ] All unit and integration tests pass
- [ ] Amounts are stored as integer cents — no floating-point values in the database
