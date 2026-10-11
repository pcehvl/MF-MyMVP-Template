# Bootstrap Verification

Bootstrap is technical setup, not a User Story. This file records the actual
results of configuring and verifying the harness.

## Environment

| Item | Value |
|---|---|
| Date / time | 2026-10-05 08:36:25 +02:00 |
| OS | Windows (win32) |
| Node.js | v24.21.0 |
| npm | 12.0.2 |
| Git | 2.56.0.windows.1 |
| GitHub CLI | 2.94.0 (authenticated; `repo`, `workflow` scopes) |
| OpenCode | v2.0.11 |
| Vercel CLI | 59.23.2 |
| Supabase CLI | **not installed** (human action required) |
| Repository state | Genuinely new: empty directory, no prior Git repository, no remote |

## Source of the scaffold

No fixed starter was supplied. The frontend shell was generated with
`create-vite@9.2.1` (`react-ts` template, ESLint, non-interactive) in a
project-local temporary directory, then moved into the repository root and
customised. **Generated scaffold files may vary across independent runs**; the
lockfile (`package-lock.json`) is the reproducibility anchor from here on.

The shadcn/ui setup was produced by `shadcn@4.21.1 init` (base `radix`, preset
`nova` — Lucide/Geist, CSS variables, no RTL, no pointer cursor) followed by
`shadcn add button`.

## Checks run (final state, all after last code change)

| Check | Command | Result |
|---|---|---|
| Lint | `npm run lint` | **PASS** (exit 0) |
| Typecheck | `npm run typecheck` (`tsc -b`) | **PASS** (exit 0) |
| Unit / component tests | `npm run test -- --run` | **PASS** — 1 file, 1 test (`src/App.test.tsx`) |
| Production build | `npm run build` | **PASS** — 2003 modules, `dist/` emitted |
| E2E (local Playwright) | `npm run test:e2e` | **PASS** — 1 test, chromium |
| Full sequence | `npm run verify` | **PASS** (exit 0) — lint → typecheck → tests → build → E2E |
| Dependency audit | `npm audit` | **PASS** — 0 vulnerabilities |

`npm run verify` was executed after the final source, configuration and
dependency changes (theme inlining, shadcn CLI removal, Vitest scoping) and
completed with exit code 0.

## Acceptance Criterion-to-evidence mapping

Bootstrap has no User Story and therefore no product Acceptance Criteria. The
technical checks above are the verification evidence for the harness itself.

## E2E, persistence, migration and access-control results

| Area | Result |
|---|---|
| E2E critical journey | PASS — application shell renders, heading and primary action visible (accessible role/name selectors) |
| Persistence | NOT RUN — no Story has required data yet; `supabase/migrations/` is intentionally empty |
| Migrations | NOT RUN — no migrations exist; none required by bootstrap |
| Access-control (RLS) | NOT RUN — no Supabase project is linked yet; nothing to test against |

These are not failures: bootstrap created no schema and no data paths. They
become required checks when the first Story that touches data is implemented.

## Checks not run, and why

| Check | Reason |
|---|---|
| Supabase migration / RLS verification | Supabase CLI not installed and no remote project linked; human action required (see `docs/deployment.md`) |
| Deployment | Explicitly out of scope for bootstrap ("Do NOT deploy yet"); no Vercel or Supabase project exists |
| Remote GitHub operations (push, PR) | Not authorised during bootstrap; repository has no remote |

## Security checks performed

- `npm audit`: 0 vulnerabilities (after removing the `shadcn` CLI from the dependency tree; see below)
- No secrets committed: `.gitignore` excludes `.env`, `.env.*` (keeping `.env.example`); `.env.example` contains placeholders only
- No privileged credentials in browser code: `src/lib/supabase.ts` accepts only `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` (public client values), and creates the client only when both are present
- No committed credentials found in the working tree

## Findings and resolutions during bootstrap

1. **TypeScript 7 incompatible with typescript-eslint.** `typescript-eslint@8.71.0`
   peers require `typescript >=4.8.4 <6.1.0`. Resolved by pinning
   `typescript@~6.0.2` (latest compatible release).
2. **shadcn CLI workspace-config failure.** `shadcn init` failed with
   "Could not load the workspace config" because the root `tsconfig.json`
   (Vite template solution-style file) carried no `compilerOptions.paths`, so
   shadcn could not resolve `@/*` aliases (its documented requirement). Resolved
   by adding the `@/*` path mapping to the root `tsconfig.json`. TypeScript 6
   deprecates `baseUrl`, so the mapping is declared without it; both `tsc` and
   the shadcn CLI resolve paths relative to the tsconfig location (verified:
   `tsc -b` passes and `shadcn add` resolves aliases).
3. **Vitest picked up Playwright specs.** Default Vitest discovery matched
   `e2e/*.spec.ts`, causing "Playwright Test did not expect test() to be called
   here". Resolved by scoping Vitest `include` to `src/**` and excluding `e2e/`.
4. **react-refresh lint error on shadcn primitives.** `button.tsx` exports
   `buttonVariants` alongside the component, which the Vite react-refresh rule
   flags. Resolved with a scoped rule disabling `react-refresh/only-export-components`
   for `src/components/ui/**` only (standard shadcn composition pattern).
5. **shadcn CLI transitive vulnerabilities.** `npm audit` reported 7 high
   severity issues, all tracing to the `shadcn` CLI package (braces /
   micromatch / fast-glob / ts-morph — a glob stack-exhaustion DoS in the CLI
   toolchain, not the app runtime). Two mitigations applied:
   - the CLI was removed from `devDependencies`; component additions use the
     pinned `npx shadcn@4.21.1 add <component>` path (verified working);
   - the CLI's theme file (`shadcn/tailwind.css`, required at build time by
     `src/index.css`) was vendored into `src/index.css` with a provenance
     comment. Build output is byte-identical to the pre-vendoring build
     (same CSS hash), confirming a faithful inline.
   Result: `npm audit` reports 0 vulnerabilities.
6. **shadcn init prompts in non-TTY mode.** `init` prompts for preset and
   reinstall despite `-y`; resolved by passing explicit flags
   (`--preset nova --no-reinstall --force`).

## Definition-of-DoD status for bootstrap

| Requirement | Status |
|---|---|
| Harness rules, agents, skills, structure created | PASS |
| OpenCode config/frontmatter validated against v2.0.11 | PASS — `opencode debug agents` lists all three agents with modes and permissions; all six skills discovered |
| React + TypeScript + Vite configured | PASS |
| Tailwind + shadcn/ui + Lucide configured | PASS |
| Supabase client + empty migration structure | PASS — client created (inert without env); `supabase/migrations/` and `supabase/functions/` present and empty |
| Vitest + React Testing Library configured | PASS |
| Playwright configured | PASS — `webServer` + `baseURL` set, chromium installed |
| Product-neutral smoke test only | PASS — shell renders, no product content invented |
| PR template per contract | PASS — `.github/pull_request_template.md` (no Issue references or closing keywords) |
| Local verification commands + `npm run verify` | PASS |
| Vercel-compatible deployment prep + docs | PASS — `docs/deployment.md` written; deployment NOT performed (out of scope) |
| `.env.example` + `.gitignore` protect secrets | PASS |
| Dependencies installed via npm (lockfile created and versioned) | PASS — `package-lock.json` committed |
| Lint / typecheck / unit tests / build / E2E / verify | ALL PASS |
| No GitHub Issues, workflows, secrets or hosted CI created | PASS |
| No fictional Stories, requirements or schema created | PASS |
| No deployment performed | PASS |

## Local changes versus remote operations

**Local only.** Git repository initialised on `main`; an initial local baseline
commit establishes `main`. Nothing was pushed, no remote repository was created,
no Pull Request was opened and no deployment was performed — all require human
authorisation.

## Reproducibility notes

- Generated scaffold files may vary across independent runs (no fixed starter
  was supplied); `package-lock.json` pins the resolved dependency tree.
- Playwright browsers are installed per machine (`npx playwright install
  chromium`).
- The shadcn theme is vendored in `src/index.css`; when upgrading shadcn,
  re-inline `dist/tailwind.css` from the new version and re-run `npm run verify`.
