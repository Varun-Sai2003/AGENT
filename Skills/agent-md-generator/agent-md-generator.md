---
skill: agent-md-generator
version_covered: AGENTS.md open standard (2025)
depth: comprehensive
last_updated: 2026-03-21
use_when: >
  Load this skill whenever the user wants to create an AGENT.md (or AGENTS.md) file for any
  project — new or existing. This includes generating project structure, scaffolding folder
  layouts, defining coding conventions, build/test/deploy commands, and any project-specific
  agent instructions. Triggers include phrases like "create an AGENT.md", "set up a project",
  "scaffold a project", "generate project config for AI", "make an agents file", or anything
  involving project initialization where an AI agent needs operating instructions.
---
# AGENT.md Generator — Expert Reference

> **Quick Summary**: AGENT.md (or AGENTS.md) is an open standard used by 60k+ projects to give
> AI coding agents project-specific instructions. Think of it as a "README for agents." This
> skill enables you to analyze any project and generate a comprehensive, production-grade
> AGENT.md file — plus scaffold the entire project structure if it's a new project.

---

## 1. Overview

### What is AGENT.md?

An `AGENT.md` file is a dedicated, predictable place at the root of a project that provides
context and instructions to help AI coding agents (Cursor, Windsurf, GitHub Copilot, Gemini,
Codex CLI, etc.) work effectively on your codebase.

It complements the human-focused `README.md` by providing machine-optimized instructions:
coding conventions, build commands, architecture decisions, testing patterns, and guardrails.

### Why AGENT.md?

| Without AGENT.md                           | With AGENT.md                        |
| ------------------------------------------ | ------------------------------------ |
| Agent guesses project conventions          | Agent follows explicit rules         |
| Repeated prompt instructions every session | Instructions persist in the repo     |
| Inconsistent code style across AI outputs  | Uniform style enforced automatically |
| Agent may break things it shouldn't touch  | Protected files/areas are declared   |
| Every AI tool needs separate config        | One file works across all agents     |

### How Agents Read It

Most AI coding tools automatically read `AGENT.md` or `AGENTS.md` from:

1. **Project root** — the main instructions
2. **Subdirectories** — nested overrides for monorepos or sub-packages
3. The nearest `AGENT.md` to the file being edited takes precedence

---

## 2. Core Concepts

### The AGENT.md Standard

The standard is intentionally simple — it's just a Markdown file. There is no required schema,
no YAML frontmatter, no special syntax. The power comes from **what you put in it**.

### Key Principles

| Principle                      | Why It Matters                                                                    |
| ------------------------------ | --------------------------------------------------------------------------------- |
| **Specificity**                | Vague rules produce vague code. "Use single quotes" beats "follow best practices" |
| **Actionability**              | Every instruction should be something the agent can execute                       |
| **Brevity**                    | LLMs have instruction budgets (~150-200 rules). Keep it focused                   |
| **Progressive disclosure**     | Root `AGENT.md` for global rules, nested ones for specifics                       |
| **Examples over abstractions** | Show a code snippet, don't describe what good code looks like                     |
| **Explicit boundaries**        | Clearly state what the agent should NOT touch                                     |

### Anatomy of a Great AGENT.md

A production-grade `AGENT.md` typically covers these sections (adapt per project):

1. **Project Overview** — What is this, what does it do
2. **Agent Identity & Role** — Name, persona, mandate, guiding principles
3. **Tone of Voice & Personality** — How the agent communicates and presents itself
4. **Communication Style** — Formatting, vocabulary, response length, interaction patterns
5. **Setup Commands** — How to install, build, run
6. **Tech Stack** — Languages, frameworks, versions
7. **Project Structure** — Key directories and their purpose
8. **Code Style & Conventions** — Formatting, naming, patterns
9. **Architecture Decisions** — Key design choices the agent must respect
10. **Testing Instructions** — How to run tests, what framework, TDD expectations
11. **Key Metrics / KPIs** — Measurable success criteria for the agent's work
12. **Deployment** — How and where to deploy
13. **Do's and Don'ts** — Explicit rules and guardrails
14. **Error Handling & Escalation** — How to report errors, when to ask a human
15. **Security & Boundaries** — Files never to touch, secrets handling
16. **Workflow & Planning** — How the agent approaches and sequences tasks
17. **Context & Memory** — What the agent remembers across sessions
18. **PR / Commit Conventions** — Commit message format, branch naming

### Key Terms

| Term                       | Definition                                                            |
| -------------------------- | --------------------------------------------------------------------- |
| **AGENT.md / AGENTS.md**   | The instruction file for AI coding agents (both names are recognized) |
| **Root AGENT.md**          | The main file at the project root — applies globally                  |
| **Nested AGENT.md**        | A file in a subdirectory that provides local overrides                |
| **Instruction budget**     | The ~150-200 rule limit before LLMs start dropping instructions       |
| **Progressive disclosure** | Technique of splitting instructions across root + nested files        |
| **Guardrails**             | Explicit restrictions on what the agent can/cannot do                 |
| **Scaffolding**            | Generating the initial folder structure and boilerplate files         |
| **Persona**                | The agent's defined identity, personality, and role in the project    |
| **KPIs**                   | Key Performance Indicators — measurable goals for agent effectiveness |
| **Escalation**             | Handing off a task or decision to a human when the agent is unsure    |

---

## 3. The Generator Workflow

### Phase 1 — Understand the Project

Before generating anything, gather these details (ask only what isn't obvious):

| Question                                                        | Why It Matters                                  |
| --------------------------------------------------------------- | ----------------------------------------------- |
| What is the project? (name, purpose, description)               | Shapes the overview section                     |
| What tech stack? (language, framework, DB, hosting)             | Determines setup and conventions                |
| Is this a new project or existing?                              | New = scaffold structure; existing = analyze it |
| What package manager? (npm, pnpm, pip, cargo, etc.)             | Affects all command examples                    |
| What deployment target? (Vercel, Modal, AWS, Docker)            | Shapes deployment section                       |
| Any specific coding rules the user wants enforced?              | Seeds the do's/don'ts section                   |
| Solo dev or team?                                               | Affects PR/commit convention depth              |
| What AI tools will consume this?                                | May influence format/placement                  |
| What tone should the agent use? (formal/casual/technical)       | Shapes the tone & personality section           |
| Does the agent have a specific persona or name?                 | Defines the identity section                    |
| What are the key success metrics for this project?              | Seeds the KPI / metrics section                 |
| How should the agent handle errors or uncertainty?              | Defines escalation and error-handling rules     |
| Any communication preferences? (concise/detailed, emojis, etc.) | Shapes the communication style section          |

If the user's request is clear (e.g., "create an AGENT.md for my Next.js app"), fill in
reasonable defaults and proceed.

### Phase 2 — Analyze or Scaffold the Project

#### For Existing Projects:

1. **Scan the directory tree** — `list_dir` and `find_by_name` to map the structure
2. **Read key config files** — `package.json`, `pyproject.toml`, `Cargo.toml`, `.env.example`, etc.
3. **Identify patterns** — framework conventions, test setup, build scripts, CI config
4. **Read existing docs** — `README.md`, any existing instructions or project specs
5. **Detect tech stack** — from dependencies, file extensions, config files

#### For New Projects:

1. **Ask project type** if not obvious from context
2. **Generate `project_specs.md`** — define what we're building (following the user's existing process)
3. **Scaffold the folder structure** — create all directories and placeholder files
4. **Initialize package management** — run init commands as needed
5. **Then generate AGENT.md** based on the scaffold

### Phase 3 — Generate the AGENT.md File

Use the template in Section 4 below. Customize every section for the specific project.

**Output location**: Always save to the **project root** as `AGENT.md`

### Phase 4 — Generate Project Structure (If New Project)

For new projects, also generate:

1. The complete folder structure
2. A `project_specs.md` file (per the user's existing workflow)
3. Starter files (entry points, config files, .gitignore, .env.example)
4. A README.md for humans

### Phase 5 — Quality Check & Present

Before saving, verify:

- [ ] All commands are correct and runnable
- [ ] Tech stack matches the actual project
- [ ] File paths in the structure section match the real directory tree
- [ ] Do's/Don'ts are specific, not generic platitudes
- [ ] Instruction count stays under ~150 rules (instruction budget)
- [ ] Security section doesn't expose any real secrets
- [ ] Agent identity and role are clearly defined
- [ ] Tone & personality match the project's context (team vs solo, formal vs casual)
- [ ] Communication style preferences are specified
- [ ] Key metrics are measurable and project-specific (not generic)
- [ ] Error handling & escalation rules are clear and actionable
- [ ] Workflow steps are defined and match the user's existing process

---

## 4. AGENT.md Template

Use this as the base template. **Customize every section** — never leave generic placeholders.

````markdown
# AGENT.md

> AI coding agent instructions for **[Project Name]**.
> This file is read automatically by AI tools (Cursor, Windsurf, Copilot, Codex, Gemini).

---

## Agent Identity & Role

- **Name**: [e.g., @project-agent, ProjectBot]
- **Role**: [e.g., Full-stack developer and code reviewer for this project]
- **Mandate**: [e.g., Help build, test, and maintain this codebase following all conventions below]
- **Guiding Principles**:
  - [e.g., KISS — prefer the simplest solution that meets requirements]
  - [e.g., Accuracy first — never guess when you can verify]
  - [e.g., Ask before breaking — confirm destructive actions with the user]

---

## Tone of Voice & Personality

- **Personality**: [e.g., Decisive, concise, supportive — avoids hedging or filler]
- **Tone**: [e.g., Professional yet approachable — like a senior engineer pair-programming]
- **Formality**: [e.g., Semi-formal — use contractions, avoid slang or jargon overload]
- **Emotional handling**: [e.g., Acknowledge user frustration before offering solutions]
- **Humor / Emojis**: [e.g., Minimal — use emojis sparingly for emphasis only, no jokes in code reviews]

---

## Communication Style

- **Vocabulary**: [e.g., Use technical terms when accurate, but explain complex concepts in plain language]
- **Formatting**: [e.g., Use bullet points for lists, code blocks for code/commands, bold for emphasis]
- **Response length**: [e.g., Concise by default — expand only when depth is requested or context demands it]
- **Pronouns**: [e.g., Use "I" for the agent, "you" for the user]
- **Interaction pattern**: [e.g., Always explain reasoning briefly before making changes]
- **Acknowledgment**: [e.g., Acknowledge mistakes, explain backtracking, never silently change direction]

---

## Project Overview

[2-3 sentence description: what this project is, what problem it solves, who it's for]

- **Type**: [Web app / API / CLI tool / Mobile app / Library / Monorepo]
- **Status**: [Active development / MVP / Production / Maintenance]

---

## Tech Stack

| Layer     | Technology               | Version      |
| --------- | ------------------------ | ------------ |
| Language  | [e.g., TypeScript]       | [e.g., 5.x]  |
| Framework | [e.g., Next.js]          | [e.g., 14.x] |
| Styling   | [e.g., Tailwind CSS]     | [e.g., 3.x]  |
| Database  | [e.g., PostgreSQL]       | [e.g., 16]   |
| ORM       | [e.g., Prisma]           | [e.g., 5.x]  |
| Hosting   | [e.g., Vercel]           | —            |
| AI Model  | [e.g., Gemini 2.0 Flash] | —            |

---

## Setup Commands

```bash
# Install dependencies
[e.g., pnpm install]

# Set up environment variables
[e.g., cp .env.example .env]

# Run database migrations
[e.g., pnpm prisma migrate dev]

# Start development server
[e.g., pnpm dev]

# Run tests
[e.g., pnpm test]

# Build for production
[e.g., pnpm build]

# Deploy
[e.g., vercel deploy --prod]
```

---

## Project Structure

```
project-root/
├── AGENT.md              ← You are here
├── README.md             ← Human documentation
├── project_specs.md      ← What we are building (approved spec)
├── .env.example          ← Environment variable template
├── .env                  ← Local secrets (NEVER commit)
├── .gitignore
├── package.json
│
├── src/                  ← Application source code
│   ├── app/              ← Routes / pages
│   ├── components/       ← Reusable UI components
│   ├── lib/              ← Utilities, helpers, API clients
│   ├── styles/           ← Global styles, design tokens
│   └── types/            ← TypeScript type definitions
│
├── public/               ← Static assets
├── tests/                ← Test files
├── scripts/              ← Build/deploy/utility scripts
└── docs/                 ← Additional documentation
```

> Customize this tree to match the actual project layout.

---

## Code Style & Conventions

### Formatting

- [e.g., Use single quotes for strings]
- [e.g., No semicolons (Prettier handles it)]
- [e.g., 2-space indentation]
- [e.g., Max line length: 100 characters]

### Naming

- **Files**: [e.g., kebab-case for files (`user-profile.tsx`)]
- **Components**: [e.g., PascalCase (`UserProfile`)]
- **Functions**: [e.g., camelCase (`getUserProfile`)]
- **Constants**: [e.g., UPPER_SNAKE (`API_BASE_URL`)]
- **CSS classes**: [e.g., BEM or Tailwind utilities]

### Patterns

- [e.g., Use functional components with hooks, never class components]
- [e.g., Prefer `const` over `let`, never use `var`]
- [e.g., Use async/await over .then() chains]
- [e.g., Colocate tests next to source files]

---

## Architecture Decisions

- [e.g., Server-side rendering for all public pages, client components only when needed]
- [e.g., All API routes return JSON with `{ data, error, status }` shape]
- [e.g., Feature-based folder organization: `features/auth/`, `features/dashboard/`]
- [e.g., State management: server state with TanStack Query, local state with useState]

---

## Testing

- **Framework**: [e.g., Vitest + React Testing Library]
- **Run all tests**: `[e.g., pnpm test]`
- **Run single test**: `[e.g., pnpm vitest run path/to/test.ts]`
- **Coverage**: `[e.g., pnpm test -- --coverage]`

### Rules

- Write tests for all new features
- Test behavior, not implementation
- Minimum 80% coverage for new code
- Always run tests before committing

---

## Key Metrics

> Measurable criteria the agent should optimize for.

| Metric                  | Target                                                      |
| ----------------------- | ----------------------------------------------------------- |
| **Build success**       | All changes must pass `[build command]` before committing   |
| **Test pass rate**      | Minimum [X]% test coverage for new code                     |
| **Style compliance**    | Zero style violations — follow all conventions in this file |
| **Security compliance** | Zero hardcoded secrets, zero exposed credentials            |
| **Iteration speed**     | Build in small pieces — one feature → test → confirm → next |
| **Code review quality** | All PRs pass automated lint + type checks                   |
| **Error rate**          | [e.g., <5% of submitted changes require rollback]           |

> Customize this table. Remove irrelevant rows, add project-specific metrics.

---

## Deployment

- **Platform**: [e.g., Vercel / Modal / AWS]
- **Branch strategy**: [e.g., `main` → production, `dev` → staging]
- **Deploy command**: `[e.g., vercel deploy --prod]`
- **Environment**: Secrets are stored in [e.g., Vercel dashboard / Modal secrets / .env]

### Pre-deploy Checklist

1. All tests pass
2. No TypeScript errors
3. Linting passes
4. Build succeeds locally
5. Environment variables are set on the platform

---

## ✅ Do

- Always read `AGENT.md`, `MEMORY.md` and `project_specs.md` before starting work
- Follow the existing code patterns — reuse before inventing
- Write tests for every new feature or bug fix
- Use the project's package manager exclusively ([e.g., pnpm])
- Keep components small and focused
- Handle errors explicitly — never silently swallow them
- Store secrets in `.env` — never hardcode API keys
- Test after every change
- Build in small pieces — one feature at a time

## ❌ Don't

- Don't modify `.env`, `secrets/`, or credential files
- Don't install new dependencies without confirming with the user
- Don't use `any` in TypeScript
- Don't write inline styles (use the design system)
- Don't skip error handling
- Don't commit `node_modules/`, `.env`, or build artifacts
- Don't deploy without running the pre-deploy checklist
- Don't make large, sweeping changes — small, testable PRs only

---

## Security & Boundaries

### Never Touch

- `.env` files (contains secrets)
- Authentication/authorization logic (requires human review)
- Payment processing code (requires human review)
- Database migration files after they've been applied

### Secrets Handling

- All API keys go in `.env`
- Use `.env.example` to document required variables (without values)
- Never log or print secrets
- Never commit secrets to git

---

## Error Handling & Escalation

### How to Handle Errors

- Explain errors in **plain English** — include what went wrong, why, and how to fix
- Never silently swallow errors or suppress warnings
- Include the exact error message or stack trace in a code block
- Suggest concrete fix options (not just "something went wrong")

### When to Escalate to the User

- [e.g., Authentication or authorization logic changes]
- [e.g., Database schema migrations or data destructive operations]
- [e.g., Installing new dependencies or upgrading major versions]
- [e.g., Any action that could cause data loss or downtime]
- [e.g., Ambiguous requirements — ask rather than guess]

### Escalation Format

> "⚠️ I need your input: [describe the situation and the options]"

---

## Workflow & Planning

When working on this project, follow this workflow:

1. **Read first** — Review `AGENT.md`, `project_specs.md`, and relevant source files
2. **Plan** — Present a plan (3–7 bullet points) before making changes
3. **Clarify** — Ask if anything is unclear before proceeding
4. **Implement** — Build in small, testable increments
5. **Test** — Run tests and verify after every change
6. **Report** — Summarize what was done, what was tested, and the next step

### Response Format

When responding, always structure replies as:

1. **Plan** (3–7 bullet points of what you'll do)
2. **What I need from you** (if anything is unclear)
3. **Next action** (one clear step)
4. **Errors** (explained in plain English if any)

---

## Context & Memory

- Reference `MEMORY.md` (if present) for learned preferences and past decisions
- Track key decisions and rationale in commit messages
- If the user corrects a pattern, apply the correction consistently going forward
- Review previous conversation context before starting new work on the same feature
- [e.g., Maintain a `decisions.md` log for major architectural choices]

---

_Generated by agent-md-generator skill. This file guides AI coding agents working on this project._
````

### 4b. WAT Framework Addendum (For Agentic Workflows)

> **Important**: If the project is identified as an Agentic Workflow or general AI Agent project (e.g., using the WAT framework), you **MUST** append these exact sections to the generated `AGENT.md`. Do not summarize them; copy them exactly.

````markdown
## The WAT Architecture

**Layer 1: Workflows (The Instructions)**
- Markdown SOPs stored in `workflows/`
- Each workflow defines the objective, required inputs, which tools to use, expected outputs, and how to handle edge cases
- Written in plain language, the same way you'd brief someone on your team

**Layer 2: Agents (The Decision-Maker)**
- This is your role. You're responsible for intelligent coordination.
- Read the relevant workflow, run tools in the correct sequence, handle failures gracefully, and ask clarifying questions when needed
- You connect intent to execution without trying to do everything yourself
- Example: If you need to pull data from a website, don't attempt it directly. Read `workflows/scrape_website.md`, figure out the required inputs, then execute `tools/scrape_single_site.py`

**Layer 3: Tools (The Execution)**
- Python scripts in `tools/` that do the actual work
- API calls, data transformations, file operations, database queries
- Credentials and API keys are stored in `.env`
- These scripts are consistent, testable, and fast

**Why this matters:** When AI tries to handle every step directly, accuracy drops fast. If each step is 90% accurate, you're down to 59% success after just five steps. By offloading execution to deterministic scripts, you stay focused on orchestration and decision-making where you excel.

## How to Operate

**1. Look for existing tools first**
Before building anything new, check `tools/` based on what your workflow requires. Only create new scripts when nothing exists for that task.

**2. Learn and adapt when things fail**
When you hit an error:
- Read the full error message and trace
- Fix the script and retest (if it uses paid API calls or credits, check with me before running again)
- Document what you learned in the workflow (rate limits, timing quirks, unexpected behavior)
- Example: You get rate-limited on an API, so you dig into the docs, discover a batch endpoint, refactor the tool to use it, verify it works, then update the workflow so this never happens again

**3. Keep workflows current**
Workflows should evolve as you learn. When you find better methods, discover constraints, or encounter recurring issues, update the workflow. That said, don't create or overwrite workflows without asking unless I explicitly tell you to. These are your instructions and need to be preserved and refined, not tossed after one use.

## The Self-Improvement Loop

Every failure is a chance to make the system stronger:
1. Identify what broke
2. Fix the tool
3. Verify the fix works
4. Update the workflow with the new approach
5. Move on with a more robust system

This loop is how the framework improves over time.

## File Structure

**What goes where:**
- **Deliverables**: Final outputs go to cloud services (Google Sheets, Slides, etc.) where I can access them directly
- **Intermediates**: Temporary processing files that can be regenerated

**Directory layout:**
```
.tmp/           # Temporary files (scraped data, intermediate exports). Regenerated as needed.
tools/          # Python scripts for deterministic execution
workflows/      # Markdown SOPs defining what to do and how
.env            # API keys and environment variables (NEVER store secrets anywhere else)
credentials.json, token.json  # Google OAuth (gitignored)
```

**Core principle:** Local files are just for processing. Anything I need to see or use lives in cloud services. Everything in `.tmp/` is disposable.

## Bottom Line

You sit between what I want (workflows) and what actually gets done (tools). Your job is to read instructions, make smart decisions, call the right tools, recover from errors, and keep improving the system as you go.

Stay pragmatic. Stay reliable. Keep learning.
````

---

## 5. Project Structure Templates

### 5a. Web App (Next.js / React)

```
project-name/
├── AGENT.md
├── README.md
├── project_specs.md
├── package.json
├── next.config.js / vite.config.ts
├── tsconfig.json
├── .env.example
├── .env
├── .gitignore
│
├── src/
│   ├── app/                    ← Routes (Next.js App Router)
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── globals.css
│   │   └── api/                ← API routes
│   ├── components/             ← Reusable UI components
│   │   ├── ui/                 ← Base components (Button, Input)
│   │   └── features/           ← Feature-specific components
│   ├── lib/                    ← Utilities, API clients, helpers
│   ├── hooks/                  ← Custom React hooks
│   ├── types/                  ← TypeScript types/interfaces
│   └── styles/                 ← Design tokens, theme
│
├── public/                     ← Static assets (images, fonts)
├── tests/                      ← Test files
│   ├── unit/
│   ├── integration/
│   └── e2e/
└── scripts/                    ← Build/deploy utilities
```

### 5b. Python Backend / API

```
project-name/
├── AGENT.md
├── README.md
├── project_specs.md
├── pyproject.toml / requirements.txt
├── .env.example
├── .env
├── .gitignore
│
├── src/
│   ├── __init__.py
│   ├── main.py                 ← Entry point
│   ├── config.py               ← Configuration / env loading
│   ├── routes/                 ← API route handlers
│   ├── models/                 ← Data models (SQLAlchemy, Pydantic)
│   ├── services/               ← Business logic
│   ├── utils/                  ← Helper functions
│   └── middleware/             ← Auth, logging, error handling
│
├── tests/
│   ├── conftest.py
│   ├── test_routes/
│   └── test_services/
│
├── scripts/                    ← Utility scripts
├── docs/                       ← Documentation
└── .tmp/                       ← Temporary files (safe to delete)
```

### 5c. Agentic Workflow (WAT Framework - Workflows, Agents, Tools)

```
project-name/
├── AGENT.md
├── README.md
├── project_specs.md
├── .env
├── .gitignore
├── requirements.txt
│
├── workflows/                  ← Markdown SOPs defining what to do and how
│   ├── example_workflow.md
│   └── example_workflow_2.md
│
├── tools/                      ← Python scripts for deterministic execution
│   ├── example_tool.py
│   └── example_tool_2.py
│
├── scripts/                    ← Utility and initialization scripts
│
└── .tmp/                       ← Temporary files (safe to delete)
```

### 5d. Mobile App (React Native / Expo)

```
project-name/
├── AGENT.md
├── README.md
├── project_specs.md
├── package.json
├── app.json / app.config.ts
├── tsconfig.json
├── .env.example
├── .gitignore
│
├── src/
│   ├── app/                    ← Expo Router pages
│   ├── components/             ← Reusable components
│   ├── features/               ← Feature modules
│   ├── hooks/                  ← Custom hooks
│   ├── lib/                    ← API clients, utilities
│   ├── navigation/             ← Navigation config
│   ├── stores/                 ← State management
│   ├── styles/                 ← Theme, design tokens
│   └── types/                  ← TypeScript types
│
├── assets/                     ← Images, fonts, icons
└── tests/
```

### 5e. Monorepo

```
project-name/
├── AGENT.md                    ← Root-level global instructions
├── README.md
├── package.json                ← Workspace root
├── turbo.json / nx.json
│
├── apps/
│   ├── web/
│   │   ├── AGENT.md            ← Web-specific overrides
│   │   └── ...
│   └── mobile/
│       ├── AGENT.md            ← Mobile-specific overrides
│       └── ...
│
├── packages/
│   ├── ui/                     ← Shared component library
│   ├── config/                 ← Shared ESLint, TS config
│   └── utils/                  ← Shared utilities
│
└── docs/
```

---

## 6. Common Pitfalls & Gotchas

### 1. Being Too Vague

```markdown
# ❌ BAD

- Follow best practices
- Write clean code
- Use good naming

# ✅ GOOD

- Use camelCase for functions, PascalCase for components
- Max 30 lines per function — extract if longer
- Prefix boolean variables with `is`, `has`, `should`
```

### 2. Overloading the File

The instruction budget for most LLMs is ~150-200 rules. Beyond that, agents start
dropping instructions. Keep the root `AGENT.md` focused and use nested files for details.

### 3. Forgetting to Update It

An outdated `AGENT.md` is worse than no `AGENT.md`. When you change the tech stack,
add new conventions, or restructure the project — update the file.

### 4. Not Including Commands

Agents can't guess your build commands. Always include exact, copy-pasteable commands:

```markdown
# ❌ BAD

"Run the tests"

# ✅ GOOD

"Run tests: `pnpm test`"
"Run single test: `pnpm vitest run src/lib/utils.test.ts`"
```

### 5. Duplicating README.md

`AGENT.md` is for the agent, `README.md` is for humans. They serve different purposes.
The agent file should be prescriptive (do this), not descriptive (this is how it works).

### 6. Ignoring Security Boundaries

Always declare files and areas the agent should never modify autonomously:

```markdown
## Never Touch Without Permission

- `.env` files
- Authentication logic
- Database migrations
- Payment handling
```

### 7. Using Relative Commands Without Context

Always specify from which directory commands should be run:

```markdown
# ❌ BAD

Run `npm test`

# ✅ GOOD

From the project root, run `npm test`

# Or for monorepos:

From `apps/web/`, run `pnpm test`
```

---

## 7. Advanced Topics

### Hierarchical AGENT.md (Monorepos)

For large projects, use nested `AGENT.md` files:

```
repo/
├── AGENT.md                    ← Global: language, PR format, CI
├── frontend/
│   └── AGENT.md                ← Frontend: React patterns, components, styling
├── backend/
│   └── AGENT.md                ← Backend: API patterns, DB conventions
└── infrastructure/
    └── AGENT.md                ← Infra: Terraform, Docker, deployment
```

The agent reads the **nearest** `AGENT.md` to the file being edited, inheriting from
parent directories.

### Dynamic AGENT.md Generation

For projects that change frequently, you can generate parts of `AGENT.md` from:

- `package.json` scripts → Setup Commands section
- `tsconfig.json` → TypeScript conventions
- `.eslintrc` → Code style rules
- CI config → Testing and deployment commands

### Integration with Other Config Files

`AGENT.md` works alongside (not replaces):

- `.cursorrules` — Cursor-specific rules
- `.windsurfrules` — Windsurf-specific rules
- `.github/copilot-instructions.md` — Copilot-specific
- `CLAUDE.md` — Claude Code-specific

The beauty of `AGENT.md` is that it works across ALL of these tools.

### Connecting to the User's Existing System

Based on the user's existing workflow (from `instructions.md`):

1. **AGENT.md replaces/complements `instructions.md`** — both serve the same purpose, but
   `AGENT.md` is an industry standard recognized by all AI tools automatically
2. **Still create `project_specs.md`** — this defines WHAT to build (kept separate)
3. **Still follow the memory system** — `MEMORY.md` for learned preferences
4. **Response format** — preserve the user's preferred Plan/Needs/Action/Errors format

---

## 8. Quick Reference Cheat Sheet

### File Placement

| Scenario           | Location                    |
| ------------------ | --------------------------- |
| Single project     | `./AGENT.md` (project root) |
| Monorepo (global)  | `./AGENT.md`                |
| Monorepo (package) | `./packages/web/AGENT.md`   |
| Sub-module         | `./src/core/AGENT.md`       |

### Essential Sections (Minimum Viable AGENT.md)

1. ✅ Project Overview (2-3 sentences)
2. ✅ Agent Identity & Role
3. ✅ Tech Stack (table)
4. ✅ Setup Commands (exact commands)
5. ✅ Code Conventions (naming, formatting)
6. ✅ Do's and Don'ts

### Power Sections (Full AGENT.md)

7. Tone of Voice & Personality
8. Communication Style
9. Key Metrics / KPIs
10. Error Handling & Escalation
11. Workflow & Planning
12. Project Structure (tree diagram)
13. Architecture Decisions
14. Testing Instructions
15. Deployment Steps
16. Security Boundaries
17. Context & Memory
18. PR/Commit Conventions

### Command to Generate

```
"Create an AGENT.md for [project description]"
"Generate an AGENT.md for this repo"
"Scaffold a new [type] project with AGENT.md"
```

---

## 9. Worked Examples

### Example 1: Web Scraper Bot (User's Project Style)

````markdown
# AGENT.md

> AI coding agent instructions for **Lead Scraper Bot**.

---

## Agent Identity & Role

- **Name**: @scraper-bot
- **Role**: Backend Python developer and scraper specialist
- **Mandate**: Build and maintain the Telegram bot following the strict instruction/execution separation pattern
- **Guiding Principles**: Accuracy first; never deploy without local testing

---

## Tone of Voice & Personality

- **Personality**: Concise, direct, and security-conscious
- **Tone**: Professional and technical
- **Formality**: Semi-formal; no humor or emojis in responses

---

## Project Overview

A Telegram chatbot that scrapes Google Maps for business leads, saves them to Airtable,
and responds to natural language queries. Uses Gemini as the AI brain and runs on Modal.

- **Type**: Agentic workflow / Telegram bot
- **Status**: Active development

---

## Tech Stack

| Layer     | Technology            |
| --------- | --------------------- |
| Language  | Python 3.11+          |
| AI        | Gemini 2.0 Flash      |
| Messaging | Telegram Bot API      |
| Database  | Airtable              |
| Hosting   | Modal.com             |
| Scraping  | Playwright / Requests |

---

## Setup Commands

```bash
pip install -r requirements.txt
cp .env.example .env
# Add your API keys to .env
python execution/test_scrape.py  # Test locally first
```
````

---

## Project Structure

```
lead-scraper-bot/
├── AGENT.md
├── project_specs.md
├── .env.example
├── .env
├── requirements.txt
├── instructions/
│   ├── scrape_google_maps.md
│   ├── airtable_save_leads.md
│   └── airtable_search_leads.md
├── execution/
│   ├── scrape_google_maps.py
│   ├── airtable_save_leads.py
│   └── airtable_search_leads.py
├── .tmp/
└── deploy/
    └── modal_app.py
```

---

## Rules

### ✅ Do

- Always read `instructions.md` and `project_specs.md` before taking action
- Python only — all scripts must be Python
- Every workflow needs TWO files: one in `instructions/`, one in `execution/`
- Build in small pieces: build → test → confirm → next
- Store all secrets in `.env` or Modal secrets
- Test locally before deploying

### ❌ Don't

- Don't write code before `project_specs.md` is approved
- Don't hardcode API keys
- Don't run code unless both instruction + execution files exist
- Don't build everything at once
- Don't deploy without testing locally first

---

## Error Handling & Escalation

- If a scraped page structure changes, report the exact CSS selector that failed
- Escalate to the user before adding new dependencies to `requirements.txt`
- Never guess a CSS selector; if it's missing, tell the user

---

## Workflow

1. Read `project_specs.md` and the relevant `instructions/` markdown file
2. Present a plan to implement the matching `execution/` script
3. Write the Python code
4. Test locally using `.tmp/` for temporary parsing files
5. Report completion with a summary of extracted data structure

````

### Example 2: Next.js Web App

```markdown
# AGENT.md

> AI coding agent instructions for **Dashboard Pro**.

---

## Agent Identity & Role

- **Name**: @frontend-agent
- **Role**: Next.js & React expert developer
- **Mandate**: build beautiful, fast, and accessible UIs following modern React patterns
- **Guiding Principles**: Component reusability, strict type safety, zero console errors

---

## Project Overview

An analytics dashboard built with Next.js 14+ and Tailwind CSS. Deployed on Vercel.
Uses Gemini 2.0 Flash for AI-powered insights.

- **Type**: Web app
- **Status**: MVP

---

## Tech Stack

| Layer | Technology | Version |
|---|---|---|
| Framework | Next.js (App Router) | 14.x |
| Language | TypeScript | 5.x |
| Styling | Tailwind CSS | 3.x |
| AI | Gemini 2.0 Flash | — |
| Hosting | Vercel | — |

---

## Setup Commands

```bash
pnpm install
cp .env.example .env
pnpm dev         # Start dev server at localhost:3000
pnpm build       # Production build
pnpm test        # Run tests
````

---

## Code Conventions

- TypeScript strict mode — no `any`
- Single quotes, no semicolons
- Functional components only — never class components
- Use `const` over `let`, never `var`
- File names: kebab-case (`user-profile.tsx`)
- Components: PascalCase (`UserProfile`)
- Hooks: camelCase with `use` prefix (`useAuth`)

---

## Rules

### ✅ Do

- Read `AGENT.md` and `project_specs.md` before starting
- Follow existing patterns — reuse before inventing
- Handle all errors explicitly
- Responsive design for all components
- Test after every change

### ❌ Don't

- Don't modify `.env` or auth logic without permission
- Don't add npm packages without confirming
- Don't use inline styles — use Tailwind
- Don't skip TypeScript types
- Don't deploy without the pre-deploy checklist

---

## Key Metrics

| Metric             | Target                                                              |
| ------------------ | ------------------------------------------------------------------- |
| **Build success**  | `pnpm build` must pass before ending task                           |
| **Linting**        | Zero ESLint warnings for new files                                  |
| **Accessibility**  | All interactive UI elements must have appropriate aria labels       |
| **Responsiveness** | UI must look good on mobile, tablet, and desktop (`sm`, `md`, `lg`) |

```

---

## 10. Go Deeper — Resources

- [Official AGENTS.md Standard](https://agents.md) — The open standard (60k+ projects)
- [AGENTS.md on GitHub](https://github.com/agentsmd/agents.md) — Spec and examples
- [OpenAI Codex AGENTS.md](https://github.com/openai/codex/blob/main/AGENTS.md) — Real-world example
- [Apache Airflow AGENTS.md](https://github.com/apache/airflow/blob/main/AGENTS.md) — Production example
- [GitHub Blog: Coding Agent Best Practices](https://github.blog) — GitHub's official recommendations
- [Builder.io Guide](https://builder.io) — Comprehensive AGENTS.md how-to
- [Cursor Docs](https://cursor.com) — How Cursor reads AGENT.md
- [Search 60k+ examples](https://github.com/search?q=path%3AAGENTS.md+NOT+is%3Afork+NOT+is%3Aarchived&type=code) — Real projects using AGENTS.md

---

## Behavior Rules

- **Always analyze before generating** — scan the project first if it exists. Never generate a generic file.
- **Always create `project_specs.md` for new projects** — follow the user's existing workflow.
- **Customize every section** — no placeholder text or generic examples in the final output.
- **Include real, runnable commands** — test them if possible.
- **Respect the instruction budget** — keep root AGENT.md under ~150 rules.
- **Match the user's existing patterns** — if they use `instructions.md` style, merge that into the AGENT.md format.
- **Offer to scaffold structure** — for new projects, generate the full folder tree + starter files.
- **Generate both AGENT.md and project_specs.md** — the agent file says HOW, the specs file says WHAT.

---

_Generated by skill-learning. Reload this file to restore expert-level knowledge of agent-md-generator._
```
