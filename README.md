# reverse-yocket

> 🚧 **Early Development**

A consultancy platform for professionals and families moving back to India from the US (or abroad). Like Yocket — but for the reverse journey.

Covers the full return journey: job search, housing, banking setup, tax implications (FBAR/RRSP/NRE accounts), school admissions for kids, city comparisons, and community connections.

---

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | Next.js 14 (App Router) |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Database | PostgreSQL (via Prisma ORM) |
| Auth | NextAuth.js |
| Deployment | Vercel |
| Testing | Vitest + Playwright |
| Package Manager | pnpm |

---

## Getting Started

```bash
# Clone
git clone git@github.com:joshiujjwal/reverse-yocket.git
cd reverse-yocket

# Install dependencies
pnpm install

# Set up environment
cp .env.example .env.local
# Fill in DATABASE_URL, NEXTAUTH_SECRET, etc.

# Run database migrations
pnpm db:migrate

# Start dev server
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000).

### Other commands

```bash
pnpm test          # Run unit tests (Vitest)
pnpm test:e2e      # Run end-to-end tests (Playwright)
pnpm lint          # ESLint
pnpm typecheck     # tsc --noEmit
pnpm build         # Production build
pnpm db:studio     # Prisma Studio (DB GUI)
```

---

## Project Structure

```
reverse-yocket/
├── src/
│   ├── app/               # Next.js App Router pages & layouts
│   ├── components/        # Shared React components
│   ├── lib/               # Server utilities, Prisma client, helpers
│   ├── hooks/             # Custom React hooks
│   ├── types/             # TypeScript type definitions
│   └── styles/            # Global CSS
├── tests/
│   ├── unit/              # Vitest unit tests (mirrors src/)
│   ├── integration/       # API route + DB integration tests
│   └── e2e/               # Playwright browser tests
├── docs/
│   ├── spec.md            # Feature specification
│   └── adr/               # Architecture Decision Records
├── .github/
│   ├── copilot-instructions.md
│   └── instructions/      # Path-specific Copilot instructions
├── prisma/
│   └── schema.prisma      # Database schema
├── README.md
├── TODO.md
├── CLAUDE.md
└── AGENTS.md
```

---

## Contributing

1. **Read** `TODO.md` — pick the next unchecked task in the current phase
2. **Run tests first**: `pnpm test` — all green before you touch anything
3. **Write the failing test first** (red phase), then implement (green phase)
4. **Manual smoke test** the feature you built
5. **Commit** with a descriptive message; include evidence (test output, screenshot) in the PR description
6. **Update** `CLAUDE.md` or `AGENTS.md` if you learned a non-obvious convention
7. **Small, focused PRs only** — one feature or fix per PR

> No unreviewed code ships. No PRs without evidence. No skipped tests.
