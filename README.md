# NoChrome

A NY-specific resource hub for parents researching school-issued K-5 device programs. Static site, no tracking, no accounts, no cookies.

The legal wedge is **NY Education Law §2-d** (student data privacy) and 8 NYCRR Part 121, which is unusually strong and unevenly enforced.

## What's in v1

- **Landing page** with three primary tasks: look up a district, can I opt out, what's the research.
- **District lookup** with client-side filter, plus a per-district page for each of 25 districts in Columbia, Dutchess, and Greene counties.
- **Opt-out legal landscape** explainer covering §2-d, FERPA, COPPA, and the NY Distraction-Free Schools law.
- **Letter generator** — client-side, fills a calm/procedural opt-out letter from form input. Nothing leaves the browser.
- **FOIL templates** — three template letters (1:1 device policy & contracts, vendor DPAs & Parents Bill of Rights, §2-d compliance documentation).
- **Board meeting toolkit** — three 2-minute public-comment scripts plus what to ask for and what to bring.
- **Research summary** — short, sourced summary on K-5 1:1 programs, monitoring software, and the data privacy dimension.
- **Alternatives** — neutral overview of Waldorf, Montessori, forest schools, classical, homeschool co-ops.
- **Pagefind search** built at deploy time.

## Stack

- [Astro 5](https://astro.build) (static)
- Tailwind CSS 4 (via `@tailwindcss/vite`)
- [Pagefind](https://pagefind.app) for static search
- Vercel as deploy target (`vercel.json` checked in)

No backend. No CMS. No analytics. No cookies. The whole site is HTML, CSS, and a small amount of vanilla client-side JS.

## Run locally

```bash
npm install
npm run dev          # dev server at http://localhost:4321
npm run build        # build to ./dist + generate Pagefind index
npm run preview      # preview the production build
```

The dev server does not include the Pagefind search index — run `npm run build` and then `npm run preview` to test search.

## Repository layout

```
src/
  pages/                  # Astro routes
    index.astro           # Landing
    about.astro
    research.astro
    alternatives.astro
    search.astro
    board-meetings.astro
    districts/
      index.astro         # District lookup
      [slug].astro        # Per-district dynamic page
    opt-out/
      index.astro         # Legal landscape
      letter-generator.astro
    foil/
      index.astro
      policy-request.astro
      dpa-request.astro
      compliance-request.astro
  components/
    Layout.astro
    DistrictSearch.astro
    FoilTemplate.astro
  data/
    districts.json        # Seed: 25 districts in Columbia/Dutchess/Greene
    statutes.json         # §2-d, Part 121, FERPA, COPPA, Distraction-Free
    citations.json        # Research source links
  styles/
    global.css
public/
  robots.txt
```

## How to add or correct a district

Districts live in `src/data/districts.json` as one entry per district. The schema is:

```jsonc
{
  "slug": "kebab-case-id",                 // URL: /districts/<slug>/
  "name": "Official District Name",
  "county": "Columbia",                    // primary county
  "counties_served": ["Columbia"],         // for cross-border districts, list all
  "address": "...",
  "phone": "...",
  "website": "https://...",
  "device_program": {
    "active": true | false | null,
    "grades": "K-5" | null,
    "device": "Chromebook" | null,
    "vendor": "Google Workspace for Education" | null,
    "take_home": "Required for grades 3-5" | null,
    "notes": null
  },
  "data_privacy": {
    "parents_bill_of_rights_posted": true | false | "partial" | null,
    "parents_bill_of_rights_url": "https://..." | null,
    "data_protection_officer": "Name, Title" | null,
    "dpas_posted": "yes" | "no" | "partial" | "unknown",
    "last_verified": "YYYY-MM-DD"
  },
  "opt_out": {
    "documented_procedure": true | false | null,
    "notes": null
  },
  "board_contact": {
    "superintendent": "...",
    "next_meeting": "YYYY-MM-DD" | null,
    "meeting_url": "https://..."
  },
  "source_documents": [
    { "title": "...", "url": "https://..." }
  ],
  "verified": true,
  "needs_foil": ["device_program_details", "parents_bill_of_rights", ...]
}
```

### Data verification standard

Every district fact must have a public source link or a `last_verified` date. **The bar is "I can point to where this came from."** Specifically:

- **`address`, `phone`, `website`, `name`** — verified against the district's own website or NYSED data site.
- **`device_program`** — verified against board policy, AUP, parent handbook, or board meeting minutes (PDFs preferred). If only inferred from press coverage, mark a `notes` field saying so.
- **`data_privacy.parents_bill_of_rights_*`** — must point to the actual public URL on the district website. If the district has not posted one, mark `parents_bill_of_rights_posted: false` and add `parents_bill_of_rights` to `needs_foil`.
- **`opt_out.documented_procedure`** — only set to `true` if the procedure is posted publicly somewhere a parent could find it without insider help.
- **`last_verified`** — update whenever you confirm any field. Stale data is worse than no data.

When in doubt, set the field to `null` and add the relevant key to `needs_foil`. The per-district page renders `null` as "Not yet verified — needs FOIL" rather than hiding the field.

### Cross-county and cross-border districts

NY school districts often straddle county lines. List all counties in `counties_served`. Examples in the v1 seed: Ichabod Crane CSD (Columbia/Rensselaer), Pine Plains CSD (Dutchess/Columbia).

## How to contribute

1. Open a PR with the data change, the source link in the PR description, and the new `last_verified` date.
2. For brand-new districts: include the source you used (NYSED data site, NCES, the district website).
3. For corrections: include the URL you used to verify and a screenshot is welcome but not required.

## Deploy target

Vercel. `vercel.json` is checked in with sensible defaults (clean URLs, trailing slash, security headers). Vercel auto-detects Astro.

```
Framework:            Astro (auto-detected)
Build command:        npm run build
Build output:         dist
Node version:         >=20
```

The `npm run build` script runs `astro build && pagefind --site dist`, so the Pagefind search index is generated as part of the build.

To deploy:

```bash
npx vercel --prod
```

Or connect the GitHub repo at https://vercel.com/new.

## Content guardrails (carried over from the project brief)

- No quotes over 14 words from any source. Paraphrase.
- Every research claim cites a specific source.
- Every district fact has a `last_verified` date.
- No invented testimonials, no fabricated success stories.
- Tone: measured, procedural, slightly clinical. Not crusade-y. Not anti-tech.
- Disclaimers on every legal-adjacent page.

## License

To be set. Currently all rights reserved. The intent is a permissive license once a license is chosen.
