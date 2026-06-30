# CLAUDE.md — maneger-front

Guidance for Claude Code (and any AI agent) working in this repository. Read this before making changes.

## What this project is

**maneger-front** is the web frontend for the Maneger project-management system.

- **Framework:** React 18 built with **Vite 5** (`@vitejs/plugin-react`).
- **Language:** plain JavaScript / JSX. **No TypeScript** — do not add it.
- **Routing:** React Router DOM 6.
- **HTTP:** Axios, wrapped in `src/api.js` (auth + endpoints).
- **Uploads:** Supabase (`@supabase/supabase-js`) for direct file uploads.
- **UI:** Hebrew (RTL), dark/light mode via CSS variables. UI strings live in `src/strings.js`.

## Project structure

```
index.html               # entry (RTL, #root)
vite.config.js           # dev server on port 3001, auto-open
src/
  main.jsx               # React root; extracts Matriya JWT from URL hash
  App.jsx                # ~4,360-line MONOLITH: routing + pages + most logic
  LabExcelSpreadsheet.jsx
  api.js                 # axios wrapper, auth (Bearer token), endpoints
  strings.js             # Hebrew localization strings
  index.css              # global styles + CSS variables (RTL, theming)
```

`App.jsx` is a single very large file holding routes, pages, state, and business logic. Edit the relevant section in place; don't rewrite the whole file unless that is explicitly the task.

## How to run, build, and verify

```bash
npm run dev      # Vite dev server, http://localhost:3001 (auto-opens)
npm run build    # production build to dist/
npm run preview  # serve the built output locally
```

There is **no test runner and no linter** configured — `npm test` and `npm run lint` do **not** exist. Don't claim "tests pass" or "lint is clean." Verify changes by running `npm run dev` (or `npm run build` + `npm run preview`) and confirming the behavior in the browser.

## Backend / configuration

- API base: `VITE_MANEGER_API_URL` (defaults to `http://localhost:8001`).
- Supabase: `VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY` (direct uploads).
- Auth: JWT in `localStorage` key `maneger_token`, sent as `Authorization: Bearer`. A `401` triggers redirect to `/login`. A Matriya token can arrive via URL hash (`#matriya_token=...`).
- **Vite env rule:** only vars prefixed `VITE_` reach the browser, and they're embedded at **build time**. See `.env.example`. Never commit `.env`; never put real secrets in frontend env — anything shipped to the browser is public (the anon key is the only key that belongs here).

## Working agreement (the important part)

1. **Don't over-engineer.** This is a hooks-based Vite + React app. Make the smallest change that fits existing patterns — no new state libraries, no TypeScript, no UI frameworks, no dependencies unless the task truly requires it and you've said why.
2. **Follow instructions and conventions.** Functional components + hooks, React Router for navigation, Axios through `src/api.js` (don't hardcode URLs or create ad-hoc axios calls — use the configured base + env vars), and put user-facing text in `src/strings.js` to stay consistent with the Hebrew/RTL UI.
3. **Don't claim done until it's verified.** "Done" means you ran the app and confirmed the change works, not just that it looks right in the diff. If you couldn't run it, say so explicitly — never report success on unverified UI changes.
4. **Don't invent APIs.** Use only React/React-Router/Axios/Supabase methods and backend endpoints you've confirmed. Before calling a backend route, check `src/api.js` (and the maneger-back routes) for the real path and payload. Don't guess endpoint names, response fields, or props a component doesn't accept.
5. **Surface uncertainty.** If a request is ambiguous, would require broad changes across the `App.jsx` monolith, or depends on a backend endpoint you can't confirm, ask or flag rather than guessing.

## Git

- Develop on branch `claude/new-session-ydal7p`.
- Clear, descriptive commit messages. Do not open a PR unless explicitly asked.
- Never commit secrets or `.env`.
