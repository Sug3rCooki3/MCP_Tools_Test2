# Sprint 1: Project Foundation and Architecture

**References:** Letter1 Sections 2, 4.1, 6

---

## Sprint Goal

Establish the repository structure, development environment, tooling, and coding standards. After this sprint, any engineer can clone the repo, install dependencies, and start developing with a consistent setup.

---

## Implementation Tasks

1. **Initialize the monorepo or project structure**
   - Create the Next.js frontend app (TypeScript)
   - Create the Node.js backend / MCP server (TypeScript)
   - Define the folder layout (e.g., `src/`, `server/`, `shared/`, `docs/`, `tests/`)
   - Add a root `README.md` with project overview and setup instructions

2. **Configure TypeScript**
   - `tsconfig.json` for frontend (Next.js defaults + strict mode)
   - `tsconfig.json` for backend (Node.js + strict mode)
   - Shared types directory if needed

3. **Install and configure linting and formatting**
   - ESLint with TypeScript rules
   - Prettier for consistent formatting
   - Add lint and format scripts to `package.json`

4. **Install and configure testing framework**
   - Choose test runner (e.g., Vitest or Jest)
   - Configure for both frontend and backend
   - Add a placeholder test to confirm the setup works
   - Add test script to `package.json`

5. **Set up environment variable handling**
   - `.env.example` file with required variables (e.g., `GPT_API_KEY`)
   - `.env` added to `.gitignore`
   - Dotenv or Next.js built-in env loading

6. **Set up SQLite**
   - Install SQLite driver (e.g., `better-sqlite3` or `drizzle-orm` with SQLite adapter)
   - Create a database initialization script or connection utility
   - Confirm the database file path is configurable via env

7. **Install Zod**
   - Add Zod as a dependency
   - Confirm it is available in both frontend and backend

8. **Define local development workflow**
   - Scripts for: `dev` (start frontend + backend), `build`, `test`, `lint`, `format`
   - Documented in README

---

## Expected Deliverables

- Working repository with frontend and backend scaffolding
- TypeScript strict mode enabled across the stack
- ESLint + Prettier configured and passing
- Test framework configured with a passing placeholder test
- SQLite driver installed and connection utility created
- Zod installed
- `.env.example` with documented variables
- README with setup and development instructions

---

## Dependencies

- None. This is the first sprint.

---

## Risk Areas

- Monorepo vs. separate packages: decide early to avoid restructuring later. For MVP, a simple folder-based separation (not a full monorepo tool like Turborepo) is sufficient.
- Next.js version: pin the version to avoid breaking changes mid-build.

---

## Test Tasks

- Placeholder unit test runs and passes
- `npm run lint` passes with zero errors
- `npm run build` completes for both frontend and backend
- SQLite connection utility can create and connect to a database file
- Environment variables load correctly from `.env`

---

## Done Criteria

- [ ] Repo structure exists and is documented
- [ ] `npm install` succeeds cleanly
- [ ] `npm run dev` starts both frontend and backend
- [ ] `npm run test` runs and passes
- [ ] `npm run lint` passes
- [ ] SQLite connects successfully
- [ ] `.env.example` documents all required variables
- [ ] README covers setup, scripts, and project overview
