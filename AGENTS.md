# AGENTS.md — reverse-yocket

Instructions for AI coding agents (OpenAI Codex, GitHub Copilot, etc.).

---

## Setup

```bash
# Prerequisites: Node 20+, pnpm 9+, Docker (for local Postgres)
pnpm install
cp .env.example .env.local   # fill in DATABASE_URL, NEXTAUTH_SECRET, etc.
docker compose up -d          # start local Postgres
pnpm db:migrate               # apply schema
pnpm db:seed                  # seed cities + guide stubs
pnpm dev                      # start dev server at localhost:3000
```

Run `pnpm test` immediately after setup. All tests must be green before starting any work.

---

## Code Style — TypeScript / Next.js

- **TypeScript strict mode** — `"strict": true` in `tsconfig.json`. No `any`.
- **Named exports** only — no default exports except for Next.js `page.tsx` and `layout.tsx` files.
- **File naming**: `kebab-case.ts` for utilities, `PascalCase.tsx` for components.
- **Imports**: path aliases configured — use `@/components/...`, `@/lib/...`, `@/types/...`. Never use relative `../../` for cross-folder imports.
- **Zod** for all API input validation — schema first, infer TypeScript type from schema.
- **Prisma generated types** for all DB entities — do not re-declare DB types manually.
- **Error handling**: API routes return `{ error: string }` with appropriate HTTP status codes. Never expose stack traces.
- **Async/await** over `.then()` chains. Always handle errors with `try/catch` in API routes.

### Component conventions
```tsx
// ✅ Good
export function CityCard({ city }: { city: City }) { ... }

// ❌ Bad
export default function CityCard({ city }: { city: any }) { ... }
```

### API route conventions
```typescript
// ✅ Every protected API route starts with this
const session = await getServerSession(authOptions)
if (!session) return NextResponse.json({ error: "Unauthorized" }, { status: 401 })

// ✅ Validate before using
const result = CityCompareSchema.safeParse(await request.json())
if (!result.success) return NextResponse.json({ error: result.error.flatten() }, { status: 400 })
```

---

## Testing

**Red/Green TDD is required.** Agents must follow this order:

1. Write failing test in `tests/unit/` or `tests/integration/`
2. Run `pnpm test` — confirm it fails for the right reason
3. Implement the feature
4. Run `pnpm test` — confirm it passes
5. Run `pnpm lint && pnpm typecheck` — fix any issues

### Test file location
- `tests/unit/lib/salary-calculator.test.ts` mirrors `src/lib/salary-calculator.ts`
- `tests/integration/api/cities.test.ts` tests `src/app/api/cities/route.ts`
- `tests/e2e/onboarding.spec.ts` tests the onboarding wizard flow

### Test conventions
```typescript
// Unit test example (Vitest)
import { describe, it, expect } from "vitest"
import { calculateMatchScore } from "@/lib/city-scoring"

describe("calculateMatchScore", () => {
  it("ranks Bengaluru highest for tech workers who prefer good air", () => {
    const profile = { occupation: "TECH", airQualityWeight: 0.8, ... }
    const scores = calculateMatchScore(allCities, profile)
    expect(scores[0].city.slug).toBe("bengaluru")
  })
})
```

### Do not
- Do not mock Prisma in integration tests — use the test database
- Do not test internal implementation details — test behaviour and outputs
- Do not delete tests to make the suite pass — fix the underlying code

---

## PR Instructions

Every PR must include in the description:
1. **What**: one-line summary of the change
2. **Why**: reference to the TODO.md task or spec section
3. **Evidence**: test output (`pnpm test` summary) OR screenshot for UI changes
4. **Review notes**: anything non-obvious the reviewer should know

PR size guidelines:
- One feature or fix per PR
- < 400 lines changed where possible
- Split DB migration PRs from application code PRs

---

## Architecture Decisions

Record significant decisions in `docs/adr/`. Use the template in `docs/adr/0001-template.md`. Examples of decisions that warrant an ADR:
- Choosing a job data source
- Adding a new major dependency
- Changing auth strategy
- Any deviation from the patterns in this file
