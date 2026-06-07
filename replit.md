# ARBOR — AI Study App

ARBOR is an AI-powered study companion with quiz generation, flashcards, a study planner, an AI tutor chatbot, and voice learning — all running without any login required.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080, serves `/api`)
- `pnpm --filter @workspace/arbor run dev` — run the frontend (port 24230, serves `/`)
- `pnpm run typecheck` — full typecheck across all packages
- Required env: `OPENROUTER_API_KEY` — stored as Replit secret

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- API: Express 5 (`artifacts/api-server`)
- Frontend: React + Vite + Tailwind v4 (`artifacts/arbor`)
- AI: OpenRouter API (`openai/gpt-4o-mini`) via backend proxy
- TTS: Browser Web Speech API (no external key needed)
- Data: localStorage (no database — all user data lives in the browser)
- Routing: react-router-dom v7

## Where things live

- `artifacts/api-server/src/routes/generate.ts` — POST /api/generate (OpenRouter proxy)
- `artifacts/arbor/src/api/aiClient.js` — frontend helper: `invokeLLM({ prompt, response_json_schema })`
- `artifacts/arbor/src/lib/localDb.js` — localStorage CRUD for Flashcard, Quiz, StudySession entities
- `artifacts/arbor/src/lib/queryClient.js` — TanStack Query client singleton
- `artifacts/arbor/src/pages/` — Dashboard, AIAssistant, Quizzes, Flashcards, Planner, VoiceLearning
- `artifacts/arbor/src/components/` — quiz/, flashcards/, layout/, planner/, shared/
- `artifacts/arbor/src/index.css` — ARBOR dark green theme (CSS variables)

## Architecture decisions

- **No auth** — app is fully public; no login/register flow.
- **No database** — all data (quizzes, flashcards, sessions) persists in localStorage under key `arbor_db`. Data is per-browser/device.
- **AI via backend proxy** — `OPENROUTER_API_KEY` never touches the frontend. All AI calls go through `POST /api/generate`.
- **TTS via Web Speech API** — VoiceLearning uses browser's built-in `speechSynthesis` instead of any paid TTS service.
- **JSX source files** — pages and components are `.jsx`; tsconfig has `allowJs: true, checkJs: false` so Vite bundles them while TypeScript only strictly checks `.ts/.tsx` files.

## Product

- **Dashboard** — overview stats (quizzes, flashcards, study hours) + quick nav to all tools
- **AI Tutor** — conversational chat with markdown rendering, powered by GPT-4o-mini
- **Quiz Engine** — AI-generates multi-choice quizzes on any topic; supports Easy/Medium/Hard and 3/5/10 questions
- **Flashcards** — manual + AI creation; spaced repetition review (SM-2 algorithm) with Hard/Okay/Easy rating
- **Study Planner** — schedule study sessions with date/time; toggle completion status
- **Voice Learning** — generates spoken scripts and plays them via Web Speech API

## User preferences

_Add explicit user preferences here as they arise._

## Gotchas

- localStorage data is not shared across browsers or devices.
- The API server must be running for AI features to work — if the frontend shows AI errors, check the API Server workflow.
- Do not import `@workspace/db` in the generate route — no database is used.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
