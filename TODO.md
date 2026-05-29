# reverse-yocket — Task Breakdown

## How to Use This File

Workflow per task:
1. Write tests FIRST (red phase) — commit the failing test
2. Implement until tests pass (green phase)
3. `git diff` — review manually before staging
4. Commit with a descriptive message
5. Update `CLAUDE.md` / `AGENTS.md` if you learned something non-obvious (compound loop)
6. Check this task off and move to the next

Evidence gate between phases: all tests must pass + human review before proceeding.

---

## Phase 0: Foundation ⬜

- [ ] `pnpm create next-app` with TypeScript, Tailwind, App Router, ESLint
- [ ] Add Vitest + `@vitejs/plugin-react` for unit tests
- [ ] Add Playwright for e2e tests
- [ ] Add Prisma ORM + PostgreSQL connection (local via Docker Compose)
- [ ] Add NextAuth.js (Google + email providers)
- [ ] Configure `pnpm lint`, `pnpm typecheck`, `pnpm test`, `pnpm build` scripts
- [ ] Set up GitHub Actions CI: lint → typecheck → test → build on every PR
- [ ] Write first smoke test: home page renders without crashing
- [ ] Add `.env.example` with all required env var keys (no values)
- [ ] Review AI config files (`CLAUDE.md`, `AGENTS.md`, `copilot-instructions.md`) — update if anything is wrong after setup

**Gate:** CI green, smoke test passes, `pnpm build` succeeds

---

## Phase 1: User Onboarding & Profile ⬜

- [ ] **Spec**: finalize `docs/spec.md` user profile section before writing code
- [ ] DB schema: `User` table — `id`, `email`, `name`, `currentCountry`, `targetCityInIndia`, `timeline`, `familySize`, `hasKids`, `occupation`, `createdAt`
- [ ] Failing tests: user creation, profile update, validation errors (red)
- [ ] API route `POST /api/profile` — create/update user profile
- [ ] API route `GET /api/profile` — fetch authenticated user profile
- [ ] Onboarding wizard UI (3 steps): current situation → target city → timeline
- [ ] Auth-gated dashboard shell (empty state is fine)
- [ ] Unit tests: profile validation logic
- [ ] Integration tests: API routes with test DB
- [ ] Manual test: complete onboarding flow end-to-end

**Gate:** All profile tests pass, onboarding flow demoed with screenshot

---

## Phase 2: City Comparison Engine ⬜

- [ ] **Spec**: define comparison dimensions in `docs/spec.md` before coding
- [ ] DB schema: `City` table — cost of living index, air quality index, tech job market score, international school count, safety index, connectivity score, sources/citations
- [ ] Seed data for 8 cities: Bengaluru, Mumbai, Hyderabad, Pune, Chennai, Delhi NCR, Ahmedabad, Kochi
- [ ] Failing tests: city comparison logic, scoring algorithm (red)
- [ ] API route `GET /api/cities` — list with filters
- [ ] API route `GET /api/cities/compare?cities=blr,mum` — side-by-side comparison
- [ ] City comparison UI — table + radar chart (use Recharts)
- [ ] "Best match" recommendation engine — weighted score based on user profile
- [ ] Unit tests: scoring algorithm with various weight combinations
- [ ] Integration tests: comparison API with seeded test data

**Gate:** Comparison engine returns correct rankings for 3+ test profiles

---

## Phase 3: Guides & Checklists ⬜

- [ ] DB schema: `Guide` table — `id`, `slug`, `category` (ENUM), `title`, `body` (MDX), `lastVerifiedAt`
- [ ] Categories: `BANKING`, `TAX`, `HOUSING`, `SCHOOLS`, `JOBS`, `VISA`, `COMMUNITY`
- [ ] Write initial guide content for each category (brief, factual, with source citations)
  - [ ] Banking: NRE → Resident conversion, FBAR filing deadlines, linking Aadhaar
  - [ ] Tax: RNOR status (2-year window), DTAA India-US, advance tax schedule
  - [ ] Housing: NRI property purchase rules, rental market norms, society documentation
  - [ ] Schools: CBSE vs ICSE vs IB/IGCSE comparison, admission timeline by city
  - [ ] Jobs: tech job market, salary benchmarking, notice period norms, resume indianisation
  - [ ] Visa/FRRO: OCI card, FRRO registration, driving licence conversion
  - [ ] Community: city-specific returnee WhatsApp/Slack groups, meetup resources
- [ ] `GET /api/guides` — list by category
- [ ] `GET /api/guides/[slug]` — single guide (MDX rendered)
- [ ] Search: full-text search across guides (Postgres `tsvector`)
- [ ] Failing tests for guide API routes (red → green)
- [ ] Guide listing page + single guide page with table of contents

**Gate:** All 7 category guides published, search returns relevant results

---

## Phase 4: Job Board Integration ⬜

- [ ] Research: which India job APIs are available (LinkedIn, Naukri, Indeed India) — document in `docs/adr/`
- [ ] DB schema: `JobPosting` — `id`, `title`, `company`, `city`, `salary`, `remotePolicy`, `source`, `postedAt`
- [ ] Background job (cron) to fetch/refresh postings from chosen API(s)
- [ ] Failing tests: job fetch, deduplication, staleness check (red)
- [ ] `GET /api/jobs` — filter by city, salary range, remote policy
- [ ] Job board UI with filters matching user profile preferences
- [ ] "Save job" feature (requires auth)
- [ ] Salary calculator: US TC → India equivalent (PPP-adjusted + stock liquidity adjustment)
- [ ] Unit tests: salary calculator accuracy

**Gate:** Job board shows 50+ live postings, salary calculator validated against 3 real data points

---

## Phase 5: Community & Connections ⬜

- [ ] DB schema: `ReturneeProfile` — public-facing, opt-in; city, industry, year of return, willing to chat (boolean)
- [ ] Privacy controls: users opt in, no contact info exposed — only async message via platform
- [ ] `GET /api/community` — browse returnees by city + industry
- [ ] In-platform messaging (simple, no real-time for now — email notification on new message)
- [ ] Community Q&A board — post questions, answer with upvotes
- [ ] Failing tests: message privacy rules, Q&A moderation flags (red)

**Gate:** 2+ test accounts can connect via platform without exposing PII

---

## Phase 6: Polish & Harden ⬜

- [ ] Error boundary components + user-friendly error pages (404, 500)
- [ ] Loading skeletons for all data-fetching pages
- [ ] Mobile responsiveness audit (test on 375px, 768px, 1280px viewports)
- [ ] Accessibility audit: axe-core on all pages, fix WCAG AA violations
- [ ] Rate limiting on all API routes (upstash/ratelimit)
- [ ] Input validation with Zod on all API routes
- [ ] Security headers via `next.config.ts`
- [ ] Performance: Lighthouse score ≥ 90 on home + city comparison pages
- [ ] Full Playwright e2e suite: onboarding → city compare → read guide → save job

**Gate:** Lighthouse ≥ 90, 0 axe violations, all e2e tests pass

---

## Phase 7: Ship ⬜

- [ ] Set up Vercel project + environment variables
- [ ] Set up production PostgreSQL (Supabase or Neon)
- [ ] Deploy to production, verify all env vars wired
- [ ] Set up error monitoring (Sentry)
- [ ] Set up analytics (Plausible — privacy-first)
- [ ] Smoke test production URL manually
- [ ] Write launch post draft

**Gate:** Production URL live, Sentry reporting, no console errors

---

## Parking Lot 🅿️

- Consultancy booking flow (paid 1:1 calls with returnee advisors)
- AI-powered "ask a question" (RAG over guides + community Q&A)
- Relocation cost estimator (container shipping, pet transport, vehicle import rules)
- Expat forums integration (IndiaReturns Reddit, NRI Facebook groups)
- Partner integrations: housing portals (99acres, MagicBricks), schools directory
- Mobile app (React Native / Expo)

---

## Lessons Learned 📝

_Add non-obvious findings here as you build. Examples: "Prisma doesn't support tsvector natively — use raw SQL for full-text search indexes", "NextAuth session strategy must be 'jwt' for edge runtime compatibility"_
