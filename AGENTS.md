# AGENTS.md

## Cursor Cloud specific instructions

### Overview

Roo Code is a VS Code extension monorepo managed by **pnpm** (v10.8.1) + **Turborepo**. It requires **Node.js 20.19.2** (exact version enforced via `.nvmrc` and `engines` field).

### Key commands

All commands run from repo root via Turborepo. See `package.json` scripts for the full list.

| Task        | Command                         |
| ----------- | ------------------------------- |
| Lint        | `pnpm lint`                     |
| Type check  | `pnpm check-types`              |
| Test        | `pnpm test`                     |
| Build all   | `pnpm build`                    |
| Build VSIX  | `pnpm vsix`                     |
| Webview dev | `pnpm dev` (from `webview-ui/`) |

### Gotchas

- **pnpm v10 build scripts**: After `pnpm install`, you may see "Ignored build scripts" warnings for native deps (`@tailwindcss/oxide`, `esbuild`, `better-sqlite3`, etc.). In practice, `esbuild` ships prebuilt binaries and works without running its postinstall. Lint, tests, and builds all pass without approving these build scripts.
- **Pre-commit hook** runs `lint-staged` (Prettier) + `pnpm lint`. Pre-push hook runs `pnpm check-types` and checks for changesets.
- **Test dependency**: `pnpm test` depends on `@roo-code/types#build` (Turborepo handles this automatically). The `types` package must be built before tests can run.
- **Webview dev server** (Vite) runs on port 5173 by default. The "Failed to resolve dependency: dagre" warning is benign.
- This is a VS Code extension — it cannot be fully "run" outside VS Code. The development workflow is: build/bundle the extension, then debug via VS Code's F5 launch. In a headless Cloud Agent environment, focus on lint/test/build/VSIX verification.
- **E2E tests** (`apps/vscode-e2e`) require a running VS Code instance and an `OPENROUTER_API_KEY`; skip these in CI-like environments.
- The `packages/evals` subsystem requires Docker (PostgreSQL + Redis); it is optional for core extension development.
