# GitHub Copilot Instructions — reverse-yocket

A Next.js 14 + TypeScript platform for Indian-origin professionals returning to India from the US. Think "Yocket in reverse" — covers job search, city comparison, tax/banking guides, school admissions, and community connections.

---

## Stack

- **Framework**: Next.js 14 (App Router)
- **Language**: TypeScript (strict mode, no `any`)
- **Styling**: Tailwind CSS + shadcn/ui components
- **Database**: PostgreSQL via Prisma ORM
- **Auth**: NextAuth.js (JWT strategy, Google + email providers)
- **Testing**: Vitest (unit/integration) + Playwright (e2e)
- **Package manager**: pnpm (never npm or yarn)

---

## Coding Conventions

### TypeScript
- Strict mode — `any` is banned. Use `unknown` + type narrowing.
- Infer types from Zod schemas: `type ProfileInput = z.infer<typeof ProfileSchema>`
- Use Prisma-generated types for all DB entities — do not redeclare them.
- Named exports everywhere except Next.js page/layout files.

### File & folder structure
- Components: `src/components/PascalCase.tsx`
- Utilities: `src/lib/kebab-case.ts`
- Path aliases: always `@/lib/...`, `@/components/...` — never `../../`
- API routes: `src/app/api/[resource]/route.ts`

### React / Next.js
- Server components by default. `"use client"` only when hooks/browser APIs are needed.
- Prefer Server Actions over client-side fetch for form mutations.
- Never fetch on the client what can be fetched on the server.
- Use `loading.tsx` and `error.tsx` co-located with pages.

### Styling
- Tailwind utility classes only.
- Use `cn()` from `@/lib/utils` for conditional class merging (clsx + tailwind-merge).
- Dark mode via `dark:` variants — never hardcode light/dark colors.

### API Routes
- Always validate request body with Zod before use.
- Always check authentication with `getServerSession(authOptions)` on protected routes.
- Return `{ error: string }` with correct HTTP status on failure — never expose stack traces.

---

## Testing Conventions

- **Write the failing test first** — always red before green.
- Unit tests in `tests/unit/` mirror `src/` structure.
- Integration tests in `tests/integration/` use a real test database.
- E2e tests in `tests/e2e/` use Playwright against localhost:3000.
- Run `pnpm test` before and after every change.

---

## Boundaries

- **Do not** refactor working code unless explicitly asked.
- **Do not** remove or skip existing tests.
- **Do not** use `any` types — not even `// @ts-ignore`.
- **Do not** add dependencies without checking `package.json` first.
- **Do not** inline SQL — always use Prisma ORM (raw SQL only for full-text search indexes).
- **Do not** store or log sensitive user data (emails, phone numbers) outside the designated DB columns.
- When in doubt about a design decision, add an ADR in `docs/adr/` instead of guessing.
