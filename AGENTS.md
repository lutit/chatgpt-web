# Repository Guidelines

This guide summarizes how to work effectively in this project. Aim for small, focused changes that match the existing patterns and style.

## Project Structure & Module Organization

- `src/`: Svelte SPA entrypoint (`App.svelte`), routing, global styles (`app.scss`).
- `src/lib/`: Core UI components, stores, utilities, and providers (`providers/openai`, `providers/petals`).
- `src-tauri/`: Tauri desktop wrapper written in Rust.
- `mocked_api/`: FastAPI-based mock OpenAI API used with `docker-compose.yml`.
- `src/awesome-chatgpt-prompts/`: Embedded prompts subproject; keep changes here minimal and self-contained.

## Build, Test & Development Commands

- `npm ci` / `npm install`: Install Node dependencies.
- `npm run dev` / `npm run dev:public`: Start Vite dev server (public binds on `0.0.0.0`, used in Docker).
- `npm run build` / `npm run build:github`: Production builds (GitHub Pages uses `/chatgpt-web/` base path).
- `npm run preview`: Preview the built site locally.
- `npm run check`: Run `svelte-check` with TypeScript.
- `npm run lint`: Run ESLint and auto-fix issues.
- `npm run tauri dev` / `npm run tauri build`: Run or build the desktop app (requires Rust + Tauri).

## Coding Style & Naming Conventions

- Use TypeScript and Svelte with 2-space indentation and single quotes; follow existing files for semicolon usage.
- Components are PascalCase (`Chat.svelte`, `ChatSettingsModal.svelte`); utility and store modules live in `src/lib`.
- Keep routing and wiring in `App.svelte`; move logic into dedicated modules where feasible.

## Testing Guidelines

- No dedicated JS test runner is configured; rely on `npm run check`, `npm run lint`, and manual testing via `npm run dev`.
- For Tauri changes, also validate with `npm run tauri dev`; add Rust tests in `src-tauri` and run `cargo test` when applicable.
- If you introduce frontend tests, colocate `*.test.ts` near the code under `src/lib`.

## Commit & Pull Request Guidelines

- Use short, descriptive, mostly imperative commit messages (e.g., `fix: update model parameters`, `chore: linting fixes`, `Bump vite from 4.5.12 to 4.5.14`).
- Keep each PR focused: describe the change, link related issues, and add screenshots/GIFs for UI changes.
- In PR descriptions, list which commands you ran (e.g., `npm run check`, `npm run lint`, `npm run build`).

## Security & Configuration Tips

- Never commit real API keys or secrets; use `.env` and `docker-compose.yml` for local configuration.
- Prefer the mock backend during development when possible (`docker-compose up` runs both the app and `mocked_api`).
- Avoid including real user data in sample chats, fixtures, or screenshots.

## Agent-Specific Instructions

- Keep diffs minimal, preserve formatting, and follow these guidelines for any files under this repository.

