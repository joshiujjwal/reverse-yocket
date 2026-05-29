# reverse-yocket — Feature Specification

**Version:** 0.1 (pre-implementation)  
**Last updated:** 2025  
**Status:** Draft — review before Phase 1 coding begins

---

## 1. Problem Statement

Every year, thousands of Indian-origin professionals in the US consider returning to India — driven by career pivots, aging parents, lifestyle preferences, or policy uncertainty. The information they need is scattered: tax forums, Facebook groups, Quora threads from 2018, and WhatsApp groups with conflicting advice.

**reverse-yocket** is the structured, trustworthy, up-to-date resource for this exact transition — mirroring what Yocket does for the India → US journey.

---

## 2. Target Users

| Persona | Description |
|---|---|
| **The Tech Returner** | 30–45, software engineer, 8–15 yrs in US, wants to return for family or lifestyle. Has RSUs/401k/ESPP to unwind. |
| **The Parent** | Returning primarily for ageing parents. Top concerns: schools for kids, healthcare, city safety. |
| **The Entrepreneur** | Wants to start something in India. Needs startup ecosystem comparison, DPIIT registration info. |
| **The Fence-Sitter** | Not decided yet. Needs honest city comparisons and community validation. |

---

## 3. Core Features

### 3.1 User Profile & Onboarding

**Functional requirements:**
- [ ] Sign up via Google OAuth or email/password
- [ ] 3-step onboarding wizard capturing: current city (US), target city in India (or "undecided"), timeline (3 months / 6 months / 1 year / 2+ years), family size, whether they have school-age kids, and occupation category
- [ ] Profile editable at any time post-onboarding
- [ ] Profile drives personalized recommendations across all features

**Non-functional:**
- [ ] Onboarding completes in < 3 minutes
- [ ] All PII stored encrypted at rest
- [ ] GDPR-compliant: users can export and delete all their data

---

### 3.2 City Comparison Engine

**Functional requirements:**
- [ ] 8 cities supported at launch: Bengaluru, Mumbai, Hyderabad, Pune, Chennai, Delhi NCR, Ahmedabad, Kochi
- [ ] Side-by-side comparison across: cost of living, air quality (AQI annual avg), tech job market depth, international school count, safety index, connectivity (flight routes to US), walkability/infrastructure
- [ ] Weighted "match score" for logged-in users based on their profile priorities
- [ ] Data sourced from public indices (Numbeo, AQI.in, AQICN, MoHUA) with citation links
- [ ] Data freshness indicator — "last verified" date per data point
- [ ] Radar chart visualization (Recharts)

**Non-functional:**
- [ ] Comparison page loads in < 2s (data cached, not fetched live)
- [ ] City data refreshed quarterly (manual process until Phase 4+)

**Data model:**

```typescript
type City = {
  id: string
  name: string
  slug: string               // "bengaluru"
  state: string
  costOfLivingIndex: number  // Numbeo, relative to Mumbai = 100
  aqiAnnualAvg: number       // µg/m³ PM2.5
  techJobScore: number       // 1–10, editorial
  intlSchoolCount: number
  safetyIndex: number        // Numbeo Crime Index (lower = safer)
  connectivityScore: number  // 1–10, based on direct US flights
  infrastructureScore: number
  sources: Record<string, string>  // { aqiSource: "URL", ... }
  lastVerifiedAt: Date
}
```

---

### 3.3 Guides & Checklists

**Functional requirements:**
- [ ] 7 guide categories (see TODO.md Phase 3 for topics)
- [ ] Each guide has a "last verified" date — stale if > 6 months
- [ ] Guides written in MDX — support callouts, tables, external links
- [ ] Full-text search across all guides
- [ ] Each guide links to primary sources (government sites, RBI circulars, CBDT)
- [ ] Checklist items are individually trackable per logged-in user (checkboxes saved to DB)

**Non-functional:**
- [ ] Search results returned in < 500ms
- [ ] All external links open in new tab with `rel="noopener noreferrer"`

---

### 3.4 Job Board

**Functional requirements:**
- [ ] Aggregated India tech job postings from ≥ 1 source API
- [ ] Filter by: city, remote policy (remote/hybrid/in-office), salary range (INR), company size
- [ ] Jobs matched to user profile by default (city preference pre-filtered)
- [ ] Salary calculator: US total comp → India equivalent
  - Inputs: US base, bonus %, RSU value/yr, location
  - Output: equivalent INR CTC, PPP-adjusted quality of life delta
- [ ] "Save" jobs (requires login); saved jobs viewable on dashboard
- [ ] Job postings expire/de-listed after 30 days unless re-fetched

**Salary calculator formula (v1):**
```
PPP_factor = 3.4  // USD/INR PPP rate (World Bank 2024)
quality_of_life_factor = user_city.costOfLivingIndex / 100

india_equivalent_INR = (us_total_comp_usd / PPP_factor) * quality_of_life_factor * 82
// 82 = nominal USD/INR exchange rate
```
> Open question: does this formula hold for high-TC engineers? Validate against community data.

---

### 3.5 Community Connections

**Functional requirements:**
- [ ] Opt-in returnee directory: city, industry, year of return
- [ ] No contact details exposed publicly — async message via platform only
- [ ] Q&A board: post question tagged by category, community answers, upvotes
- [ ] Moderation: flag system; flagged posts hidden after 3 flags, reviewed by admin

**Non-functional:**
- [ ] Message delivery via email notification (no real-time WebSocket in v1)
- [ ] No phone numbers or personal emails ever stored in the public profile

---

## 4. API Design (v1)

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/api/auth/[...nextauth]` | — | NextAuth handler |
| GET/POST | `/api/profile` | Required | Get or create/update user profile |
| GET | `/api/cities` | Optional | List all cities with scores |
| GET | `/api/cities/compare` | Optional | Compare `?cities=blr,mum,hyd` |
| GET | `/api/guides` | Optional | List guides, filter by `?category=TAX` |
| GET | `/api/guides/[slug]` | Optional | Single guide |
| GET | `/api/guides/search` | Optional | Full-text search `?q=RNOR` |
| GET | `/api/jobs` | Optional | Job listings with filters |
| POST | `/api/jobs/[id]/save` | Required | Save a job |
| GET | `/api/community` | Required | Browse returnee directory |
| POST | `/api/community/message` | Required | Send async message |
| GET/POST | `/api/qa` | Optional/Required | Q&A board |

---

## 5. Test Plan

### Unit tests
- [ ] City scoring algorithm: correct ranking for 5 synthetic profiles
- [ ] Salary calculator: known inputs → known outputs (validated against community benchmarks)
- [ ] Guide search: query returns relevant guide, not unrelated one
- [ ] Profile validation: rejects missing required fields, invalid timeline enum
- [ ] Message privacy: `sendMessage` never includes sender email in payload

### Integration tests
- [ ] `POST /api/profile` creates DB record, returns 201
- [ ] `GET /api/cities/compare` returns correct fields for 2-city request
- [ ] `GET /api/guides/search?q=RNOR` returns Tax guide in top 3
- [ ] `POST /api/jobs/[id]/save` requires auth, returns 401 if unauthenticated

### E2E tests (Playwright)
- [ ] New user: sign up → complete onboarding → land on dashboard
- [ ] Compare 3 cities → see radar chart → city match score reflects profile
- [ ] Search "RNOR" in guides → click result → guide page renders with TOC
- [ ] Save a job → navigate to dashboard → saved job visible
- [ ] Send community message → receive email notification (mocked SMTP)

---

## 6. Open Questions

| # | Question | Owner | Status |
|---|---|---|---|
| 1 | Which job API to use at launch? LinkedIn throttles heavily; Naukri has no public API. Consider scraping with Puppeteer or using Adzuna India API. | TBD | Open |
| 2 | Should guides be CMS-managed (Contentful/Sanity) or MDX files in the repo? CMS = easier content updates by non-devs; MDX = simpler for v1. | TBD | Open |
| 3 | RNOR tax window: user needs to know their RNOR status calculation — build a calculator or just explain the rules? | TBD | Open |
| 4 | Monetization model: freemium (basic guides free, city comparison paywalled)? Or subscription? Or paid 1:1 consulting sessions? | TBD | Open |
| 5 | Is city data quarterly refresh sufficient, or do we need a live AQI widget (API call on page load)? | TBD | Open |
