# StudyAI — AI-Powered Study Planner

A modern, AI-powered study planner for students. Manage subjects and exam dates, generate AI study schedules, track daily tasks, run Pomodoro sessions, view progress analytics, and chat with an AI study assistant.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080, proxied via /api)
- `pnpm --filter @workspace/study-planner run dev` — run the frontend (port 18986, proxied via /)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL`, `CLERK_SECRET_KEY`, `CLERK_PUBLISHABLE_KEY`, `VITE_CLERK_PUBLISHABLE_KEY`, `AI_INTEGRATIONS_OPENAI_BASE_URL`, `AI_INTEGRATIONS_OPENAI_API_KEY`

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React 19 + Vite 7, Tailwind CSS, shadcn/ui, Recharts, Framer Motion, Wouter
- Auth: Clerk (`@clerk/react` v6 on client, `@clerk/express` v1 on server)
- API: Express 5 with OpenAPI contract-first codegen (Orval)
- DB: PostgreSQL + Drizzle ORM
- AI: Replit OpenAI AI Integration (streaming chat, schedule generation)
- Validation: Zod (`zod/v4`), `drizzle-zod`

## Where things live

- `lib/api-spec/openapi.yaml` — OpenAPI spec (source of truth for all routes)
- `lib/api-zod/` — Generated Zod schemas from OpenAPI
- `lib/api-client-react/` — Generated React Query hooks from OpenAPI
- `lib/db/src/schema/` — Drizzle ORM schemas (subjects, tasks, schedules, studySessions, conversations, messages)
- `artifacts/api-server/src/routes/` — Express route handlers
- `artifacts/study-planner/src/pages/` — Frontend pages (dashboard, subjects, tasks, schedule, timer, analytics, chat, auth)
- `artifacts/study-planner/src/components/` — Shared UI components

## Architecture decisions

- Contract-first API: OpenAPI spec drives codegen for both server Zod validators and client React Query hooks
- Clerk proxy path (`/api/__clerk`) is only active in production; dev mode uses Clerk CDN directly
- All AI calls (chat streaming, schedule generation) go through the Replit OpenAI integration — no raw API key handling in frontend
- Theme is persisted via `next-themes` with system preference detection
- Pomodoro timer is purely client-side (no persistence needed for in-session use)

## Product

- **Dashboard**: Today's tasks, upcoming exams, study streak overview
- **Subjects**: Add/edit subjects with exam dates and color coding
- **AI Schedule**: Generate a full study schedule with OpenAI based on subjects and exam dates
- **Tasks**: Daily checklist with subject filtering and completion tracking
- **Pomodoro Timer**: Configurable work/break intervals with session logging
- **Analytics**: Progress charts (tasks completed, study time by subject, streaks)
- **AI Chat**: Streaming chat with an AI study assistant

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- `@clerk/react` v6 uses `Show` component (not `SignedIn`/`SignedOut`) for conditional rendering
- Clerk `proxyUrl` must NOT be set in development — only in production (breaks Clerk JS loading otherwise)
- `@clerk/express` and `http-proxy-middleware` must be in `dependencies` (not devDependencies) in api-server package.json
- Run `pnpm install --no-frozen-lockfile` after adding packages to package.json manually

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
- See `lib/api-spec/openapi.yaml` for all route contracts
