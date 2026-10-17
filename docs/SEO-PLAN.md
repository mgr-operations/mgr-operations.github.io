# MGR Website — SEO Plan

Status: **planned, not implemented.** This document is the audit and the ordered
work plan for making `mgr-operations.github.io` discoverable in Google, Bing and
the AI answer surfaces. Nothing in here has been applied to the site yet — every
item is a task with a "done when" check.

Companion document: [`BACKEND-PLAN.md`](BACKEND-PLAN.md) (forms, API, database).

---

## 1. Where the site stands today

Audited by reading the four live pages, not by assumption.

| Area | Finding | Verdict |
|---|---|---|
| Pages | `index.html`, `services.html`, `plans.html`, `support.html` — 4 indexable pages | Thin for the keyword set we target (§3) |
| Titles | Unique and descriptive on all 4 (`Services \| MGR ClickUp & Operations Solutions`, …) | Good |
| Meta descriptions | Unique on all 4 | Good |
| H1 | Exactly one per page | Good |
| Canonical tags | **None on any page** | Fix in Phase 0 |
| Structured data | **No JSON-LD anywhere** | Fix in Phase 0 |
| Open Graph | `og:type`, `og:title`, `og:description`, `og:image` + `twitter:card`, `twitter:image` on all 4 | Add `og:url`, `og:site_name`, `twitter:title`, `twitter:description` |
| `robots.txt` | Missing | Phase 0 (works here — see §4.4) |
| `sitemap.xml` | Missing | Phase 0 |
| Search Console / Bing Webmaster | Not verified (no verification meta or file) | Phase 0 — nothing is measurable until this exists |
| Analytics | **None** (no GA4/Plausible/Umami snippet anywhere) | Decide in §9 |
| Hero image | `Logo.png` = **1,542.8 KB**, 1536×1024, rendered above the fold, **no `width`/`height`** | Biggest single performance defect — Phase 1 |
| Page weight | index.html 16.9 KB · styles.css 26.3 KB · fonts via Google Fonts CSS (already `display=swap` + preconnect) | Acceptable once the logo is fixed |
| JavaScript | Only `support.html` (the form). Other pages ship none | Good — INP risk is near zero |
| URL shape | `.html` extensions (`/services.html`); GitHub Pages cannot rewrite or redirect them | Decision in §4.6 |
| Hosting constraint | GitHub Pages gives no access logs and no custom cache headers | Limits measurement (§9) — plan around it |
| Business facts available | "MGR Solutions", tagline "Management · Growth · Results", `support.mgr.solutions@gmail.com`, LinkedIn company URL, ClickUp credentials (VSP Certified Seller, ClickUp Expert, ClickUp Admin), one real case study (payment request 30+ min → ~4 min) | Enough for honest entity markup; **no address, phone or legal name on site** |

---

## 2. Goals and non-goals

**Goals, in priority order**

1. Be correctly indexed and canonicalised (no duplicate-URL confusion, pages crawled, sitemap known).
2. Rank for the queries where a buyer is looking to *hire someone* (§3).
3. Be quotable by AI answer engines: crawled, crawlable by their bots, and factually clear in visible HTML.
4. Convert the traffic we already get into enquiries (the email/booking CTAs and the form).

**Non-goals (deliberately excluded)**

- No keyword-stuffed city or "near me" pages — the business has no published address, and inventing one is both against Google's guidance and a real liability.
- No FAQ or HowTo structured data (retired — see §4.3).
- No self-serving review markup on our own pages.
- No mass thin blog output. The research is consistent that a few substantive pages outperform volume.
- No `llms.txt` as a visibility tactic (bonus §8.5).

---

## 3. What we are optimizing for

Queries observed in real search results for this niche, grouped by intent. **Evidence limitation: these are observed result pages, not keyword-volume or ranking data — treat them as hypotheses to validate in Search Console, not as promises.**

| Intent | Query patterns | Page that should own it | Status |
|---|---|---|---|
| Hire now | `ClickUp consultant`, `ClickUp implementation partner`, `ClickUp consultant for agencies`, `ClickUp setup service` | Dedicated implementation/consulting page | **Missing** — closest is `services.html#clickup-setup` (a fragment, cannot rank on its own) |
| Budget | `ClickUp consultant cost`, `ClickUp implementation pricing`, `ClickUp setup price` | Pricing page that separates *implementation fee* from *ClickUp subscription cost* | `plans.html` exists; needs the fee-vs-subscription clarification |
| Platform choice | `ClickUp vs Monday`, `ClickUp vs Asana for [team type]` | Honest comparison page with fit criteria | **Missing** |
| Broader operations | `workflow automation consultant small business`, `business process automation consulting`, `operations consultant small business` | Operations/process page, framed around symptoms (`manual handoffs`, `SOPs`, `client onboarding automation`) | Partially covered inside `services.html` |
| Proof | `ClickUp implementation case study`, `ClickUp agency onboarding automation` | Case study page | **Missing** — the 30-min→4-min case study currently lives as a home-page section |
| Brand | `MGR Solutions ClickUp` | Home | Covered |

The pattern across the pages that currently rank for these terms: concrete scope (discovery → mapping → build → automation → training → support), visible pricing or price ranges, named proof, and a single obvious next step. Our pages already do most of this; they are simply not organised into the URLs that match the queries.

---

## 4. Phase 0 — Technical foundation (highest value per hour)

### 4.1 Pick one canonical host and never mix it

Public URL today is the **organisation root site** `https://mgr-operations.github.io/` (repo `mgr-operations/mgr-operations.github.io`). Every canonical, sitemap entry, `og:url` and internal absolute link must use that exact form — no `www`, no `mgr-operations.github.io/index.html` variants.

If a custom domain is ever added, do it as one deliberate step and update all four pages plus the sitemap on the same day.

### 4.2 Canonical + social tags on every page

Add to each page's `<head>`, with the page's own path:

```html
<link rel="canonical" href="https://mgr-operations.github.io/services.html">
<meta property="og:url" content="https://mgr-operations.github.io/services.html">
<meta property="og:site_name" content="MGR Solutions">
<meta property="og:locale" content="en_US">
<meta name="twitter:title" content="Services | MGR ClickUp &amp; Operations Solutions">
<meta name="twitter:description" content="From process design to implementation and automation.">
```

Self-referential canonicals, absolute HTTPS URLs. Sitemap, canonical and internal links must all agree.

**Done when:** every page view-source shows a matching canonical; no page's canonical points elsewhere.

### 4.3 Structured data — what actually earns anything in 2026

Current state of the rich-result landscape (confirmed against Google's own documentation):

- **FAQPage: retired.** FAQ rich results stopped appearing in Search on **7 May 2026**, and the feature documentation was removed on **15 June 2026**. Keep the FAQ *content* on `plans.html` and `support.html` for buyers; add no markup expecting expandable results.
- **HowTo: deprecated.** Stopped appearing on desktop **13 September 2023**.
- **ProfessionalService: avoid.** Deprecated in the schema.org vocabulary because it collides with `Service`. `LocalBusiness` is only for genuine local businesses with a real location — we have no published address, so we do **not** qualify and must not fake one.
- **Service: describes, doesn't decorate.** Valid for describing visible services, but it is not a Google rich-result feature.
- **Organization: the right type for us.** Supported, needs no required properties, and helps Google identify the entity and its logo. Put it **once, on the home page**.
- **Product/Offer: no.** Do not mark consulting packages as Products to expose the price. Services are not products.
- **BreadcrumbList: yes**, matching the real (flat) navigation.

Organization JSON-LD for `index.html` — every value below is already verifiable on the site; add nothing we cannot back up:

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "@id": "https://mgr-operations.github.io/#organization",
  "name": "MGR Solutions",
  "alternateName": "MGR — Management · Growth · Results",
  "url": "https://mgr-operations.github.io/",
  "logo": "https://mgr-operations.github.io/Logo.png",
  "email": "support.mgr.solutions@gmail.com",
  "description": "ClickUp and operations consultancy: process design, workspace implementation, automation and ongoing optimization.",
  "knowsAbout": [
    "ClickUp implementation",
    "Process design and SOPs",
    "Workflow automation",
    "AI agents for operations",
    "Sales operations",
    "Dashboards and reporting"
  ],
  "contactPoint": [{
    "@type": "ContactPoint",
    "contactType": "sales",
    "email": "support.mgr.solutions@gmail.com",
    "availableLanguage": "en"
  }],
  "sameAs": ["https://www.linkedin.com/company/mgr-ops/"]
}
</script>
```

Open item: `areaServed` / `address` stay out until the owner states the real service area and legal identity (§12).

On `services.html`, describe the services that are visibly described on the page (`Service` + `provider` referencing the Organization `@id`). Do **not** mark up anything that is not visible text.

**Done when:** Schema Markup Validator reports no errors, and Rich Results Test shows Organization detected on the home page.

### 4.4 `robots.txt`

Because this is the **org root site**, `robots.txt` at the published root is the host-root file Google requires (`https://mgr-operations.github.io/robots.txt`). This would *not* work from a `/REPO/` subpath — worth knowing before anyone moves the site.

```
User-agent: *
Allow: /

Sitemap: https://mgr-operations.github.io/sitemap.xml
```

### 4.5 `sitemap.xml`

Four URLs, absolute, `lastmod` only when the page genuinely changed (Google ignores `priority` and `changefreq`):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url><loc>https://mgr-operations.github.io/</loc><lastmod>2026-10-17</lastmod></url>
  <url><loc>https://mgr-operations.github.io/services.html</loc><lastmod>2026-10-17</lastmod></url>
  <url><loc>https://mgr-operations.github.io/plans.html</loc><lastmod>2026-10-17</lastmod></url>
  <url><loc>https://mgr-operations.github.io/support.html</loc><lastmod>2026-10-17</lastmod></url>
</urlset>
```

### 4.6 The `.html` URL decision

GitHub Pages serves `/services.html` and cannot rewrite or redirect `/services` to it. Options:

| Option | Cost | Recommendation |
|---|---|---|
| Keep `.html` URLs | Zero | **Do this now.** They index fine; nothing is broken. |
| Move to `services/index.html` for clean URLs | Must move 3 files and update every internal link; old `/services.html` URLs would 404 with no server-side redirect possible until reindexed | Only if a custom domain/migration is planned anyway |

### 4.7 Verification plumbing

1. Google Search Console — verify by HTML tag or DNS; submit `sitemap.xml`; use URL Inspection on each page.
2. Bing Webmaster Tools — same, and enable the **AI Performance** report (citations across Copilot and other Microsoft surfaces).
3. Submit the sitemap in both; nothing is measurable before this step.

**Done when:** both consoles show the 4 URLs as discovered/indexed, and Search Console reports no crawl or coverage errors.

### 4.8 Keep non-pages out of the index

GitHub Pages publishes **everything in the repository root**, not a curated list. Anything committed becomes a public URL that Google can index.

Currently untracked but present in the tree: `docs/plans-pdf.html` (the print layout used to generate the pricing PDF) and `docs/MGR-Plans-Pricing.pdf`. If they are committed as-is, they become public at `https://mgr-operations.github.io/docs/…` and can appear as orphan/duplicate pages competing with `plans.html`.

Before committing them:

1. Add `<meta name="robots" content="noindex">` to the print template's `<head>` — `noindex` is the correct control for a page (a `robots.txt` `Disallow` only blocks crawling, and a disallowed page is never read, so its `noindex` would never be seen).
2. Keep the PDF out of the sitemap; it is a download, not a landing page.
3. Remember that content that exists **only** inside a PDF is invisible to search — everything important in the PDF must also exist as HTML on `plans.html` (it currently does).

**Done when:** no committed file other than the four real pages and their assets is reachable and indexable.

---

## 5. Phase 1 — Performance (Core Web Vitals)

Current Google "good" thresholds, measured at the **75th percentile of real loads**, separately for mobile and desktop: **LCP ≤ 2.5 s**, **INP ≤ 200 ms**, **CLS ≤ 0.1**. FID is gone; INP replaced it.

Our static site has almost no INP risk (one page ships JavaScript). The real risk is the hero:

| Task | Why | Done when |
|---|---|---|
| Re-export the hero logo to AVIF/WebP at ~2× display size | 1,542.8 KB is the LCP element; a well-compressed hero should land under ~120 KB | PageSpeed mobile LCP ≤ 2.5 s on a throttled run |
| Add `width`/`height` (or `aspect-ratio`) to the hero `<img>` | Removes layout shift once the image loads | CLS ≈ 0 on the home page |
| Add `fetchpriority="high"` to the hero image; `loading="lazy"` to anything below the fold | Tells the browser what to prioritise | Verified in the network panel |
| Create a dedicated **1200×630 og-image** (currently `og:image` points at the 1.5 MB `Logo.png`) | Social/AI previews fetch that URL; a 1.5 MB PNG is slow and may be rejected by some crawlers | og:image is < 300 KB, `<meta property="og:image:width" content="1200">` + height present |
| Keep the MGR fallback for missing images | Already implemented via `onerror` | No regression |
| Consider self-hosting the two font families | Removes two third-party connections and the Google CSS request | Only if a measurement shows it matters |

Known hosting limitation: GitHub Pages does not allow custom cache headers. Mitigation is smaller assets and versioned filenames (the existing `favicon.png?v=2` pattern is the right instinct).

**Done when:** Search Console's Core Web Vitals report shows the URLs as "Good" on mobile and desktop, and PageSpeed Insights mobile LCP ≤ 2.5 s.

---

## 6. Phase 2 — Content that matches buying intent

Priority order. Each new page needs the same skeleton: intent-matched H1, symptom/problem, scope, what it costs, proof, internal links, single CTA.

1. **ClickUp implementation service page** (`clickup-implementation.html`, or promoted from the `services.html#clickup-setup` section). Owns *ClickUp consultant / implementation / setup* intent. Must state fit ("teams of X–Y"), full scope (discovery → workflow mapping → workspace build → automation → training → handover), timeline, and link to `plans.html` for price.
2. **Case study page** from the existing one — the payment request workflow (30+ min → ~4 min). Structure: starting problem, what was manual, what was built (structured data, ClickUp automation, AI Super Agent), timeline, measured result, and what would differ for another team. `index.html#case-study` can then link to it in full.
3. **Comparison page**: `ClickUp vs [alternative] for [team type]` — honest fit criteria, a table, and when ClickUp is *not* the answer. Mid-funnel, and the format AI answers tend to draw from.
4. **Pricing clarifications on `plans.html`**: separate implementation/consulting fees from ClickUp's own subscription cost (searchers conflate them), and explain what changes a quote (number of teams, integrations, migration, training depth).
5. **Service pages that can stand alone** — promote the remaining `services.html` sections (automation, dashboards, training, sales operations) only if each has genuinely distinct scope, not as keyword duplicates.
6. **Industry/use-case pages** (agencies, professional services, client onboarding automation) — only where the delivery genuinely differs.

Content rules that follow from the guidance: real first-hand detail over rewritten product docs; the key facts in crawlable HTML text, not locked in the PDF; short direct answers near the question a buyer asks; lists/tables for packages and comparisons.

Internal linking: new service pages link to `plans.html` and the case study; the case study links to the service pages; home links to each new page with descriptive anchor text (not "click here").

**Done when:** each new page is in `sitemap.xml`, receives impressions in Search Console within ~4 weeks, and has at least one inbound internal link.

---

## 7. Phase 3 — Off-site entity and proof

Independent references are what make a small firm look real to both search engines and answer engines.

| Action | Notes |
|---|---|
| Complete the **ClickUp partner/verified-consultant directory** profile | Highest-relevance listing for this niche; our VSP/Expert/Admin credentials support it. Status of the application is an open question (§12). |
| Create a **Clutch** (or equivalent B2B services) profile | Relevant for consulting/implementation intent; keep the description, services and geography identical to the site. |
| Ask clients for **genuine reviews** there | Specific, in their words: problem, work done, outcome. Never buy or script them. |
| Keep **name, tagline, email and LinkedIn URL identical** everywhere | Entity consistency is what lets search engines connect the site, the directory listings and the LinkedIn page. |
| Publish **short walkthrough videos / workflow demos** | Brand mentions across the web correlate with AI-answer visibility in third-party research (Ahrefs' 75k-brand study reports ~0.74 correlation with YouTube mentions) — but that is **correlation, not causation**; treat it as a useful side effect, not a guaranteed lever. |

Do **not** rely on review markup: Google restricts self-serving review snippets for organisations, so testimonials on our own pages will not produce stars.

---

## 8. AI answer engines (AI Overviews, ChatGPT, Perplexity, Copilot)

### 8.1 What is required

Google states there are **no additional technical requirements, machine-readable AI files or special schema types** for AI Overviews / AI Mode — a page must simply be indexed and eligible to appear as a snippet in normal Search. Optimising our indexing and content covers AI Overviews automatically.

### 8.2 Crawl access for the other engines

| Bot | Purpose | Decision |
|---|---|---|
| `Googlebot` | Search + AI Overviews | Allow (default) |
| `Bingbot` | Bing + Copilot | Allow |
| `OAI-SearchBot` | Surfaces sites **in ChatGPT search** — opting out removes us from ChatGPT answers | Allow |
| `GPTBot` | Separate control tied to model training | Decide separately (§12); it is not needed for search visibility |
| `PerplexityBot` | Surfaces and links sites in Perplexity results | Allow |

Since we publish no blocking rules today, the practical task is: confirm after adding `robots.txt` that none of these are blocked (and that no CDN/firewall rule blocks by user-agent).

### 8.3 What makes a page quotable

Specific service pages, case studies with measurable outcomes, pricing pages that state real starting prices and what changes them, honest comparisons, and process/troubleshooting content from real work — with facts in visible HTML, clear headings, and short direct answers.

### 8.4 Measurement

Bing Webmaster Tools' **AI Performance** report shows citation URLs and grounding queries across Copilot and other Microsoft surfaces — it measures citations, not rank. Supplement with periodic manual spot checks of realistic buyer questions in each assistant, recorded in a small log so trends are visible.

### 8.5 `llms.txt` — not a priority

It is a community proposal, not an established crawler control: Google documents no need for AI files, OpenAI and Perplexity document their search crawlers without it, and Ahrefs' 2026 sample found **97% of valid `llms.txt` files received no requests**. Optional 10-minute addition for future tooling; not a visibility tactic.

---

## 9. Measurement plan

| Signal | Source | Note |
|---|---|---|
| Queries, impressions, CTR, average position | Google Search Console | The primary signal; segment by page |
| Indexed URLs, coverage/crawl errors | Search Console | Check after Phase 0 |
| Core Web Vitals (field, p75) | Search Console CWV report | Mobile and desktop separately |
| AI citations | Bing Webmaster Tools → AI Performance | Copilot/Microsoft surfaces |
| Referral traffic, form + email conversions | **Needs a decision** — no analytics is installed today | Recommend a lightweight privacy-friendly option; avoid a heavy tag on a 4-page static site |
| AI-bot hits | **Not measurable from GitHub Pages** — no access logs | Verify crawl *access* instead (§8.2); do not plan log analysis |

Call tracking is out of scope (no published phone number).

---

## 10. Sequenced backlog

| # | Task | Phase | Effort | Impact | Done when |
|---|---|---|---|---|---|
| 1 | Canonical + `og:url`/`og:site_name`/twitter title+description on all 4 pages | 0 | ~1 h | High | View-source matches on every page |
| 2 | Organization JSON-LD on home + BreadcrumbList on inner pages | 0 | ~1 h | Medium | Validator clean; Rich Results Test detects Organization |
| 3 | `robots.txt` + `sitemap.xml` at the published root | 0 | ~30 min | High | Both URLs return 200 live |
| 4 | Verify Search Console + Bing Webmaster, submit sitemap | 0 | ~30 min | High | 4 URLs discovered, no errors |
| 5 | `noindex` on `docs/plans-pdf.html` before it is committed | 0 | 5 min | Low | `/docs/plans-pdf.html` served with a robots noindex, absent from the sitemap |
| 6 | Compress hero logo, add dimensions + `fetchpriority` | 1 | ~1 h | High | Mobile LCP ≤ 2.5 s, CLS ≈ 0 |
| 7 | Dedicated 1200×630 og-image + `og:image:width/height` | 1 | ~30 min | Medium | og:image < 300 KB, dimensions declared |
| 8 | ClickUp implementation service page | 2 | ~4 h | High | Indexed, in sitemap, internally linked |
| 9 | Case study page (from `#case-study`) | 2 | ~3 h | High | Indexed, linked both ways with home |
| 10 | Pricing clarification (fee vs subscription, what changes scope) | 2 | ~2 h | Medium | Impressions for cost/pricing queries appear |
| 11 | Comparison page (honest fit criteria) | 2 | ~4 h | Medium | Indexed; entering mid-funnel queries |
| 12 | ClickUp partner directory profile | 3 | ~2 h + review time | High | Profile live and linked from the site's about area |
| 13 | B2B directory profile + review requests | 3 | ongoing | Medium | ≥1 live profile with genuine reviews |
| 14 | Analytics decision + install | 3 | ~1 h | Medium | Conversions visible per page |
| 15 | Industry/service pages (only if scope genuinely differs) | 3 | ~3 h each | Medium | Each earns impressions within ~4 weeks |

---

## 11. Risks and anti-patterns

- **No address, no LocalBusiness.** A consulting firm without a published service area cannot honestly claim local presence. Don't add maps, "near me" pages or a fake address to chase local results.
- **No invented facts in markup.** Prices, credentials, service area and legal name must be owner-confirmed before they appear in JSON-LD — misleading structured data violates Google's policies and, more practically, misrepresents the business.
- **No FAQ/HowTo/review markup.** Retired or restricted; adding it is wasted effort or a policy risk.
- **Thin page multiplication.** Multiple near-duplicate service pages compete with each other. Only split where scope genuinely differs.
- **`.html` URL churn.** Moving URLs without server-side redirects (impossible on GitHub Pages) throws away any rankings gained. Decide once, once only.
- **Pricing exposure.** Publishing "from" prices is a conversion and positioning decision as much as an SEO one; the current figures are placeholders pending confirmation.

---

## 12. Open decisions (owner input needed)

1. **Legal/entity name and service area** — what should `Organization` state, and should we claim a geographic area at all?
2. **Custom domain** — staying on `mgr-operations.github.io` is fine for SEO; a domain is a branding/migration project, best done before content scales.
3. **ClickUp partner directory status** — is the firm already listed/verified there? (Directly affects Phase 3 timing and what we may claim.)
4. **AI crawler policy** — allow `GPTBot` (model training) in addition to `OAI-SearchBot` (search)?
5. **Analytics** — acceptable to add a privacy-friendly analytics script, and which one?
6. **Pricing** — are the current "from $" figures approved for public indexation?

---

## Appendix — quick verification commands

Run after deploying (no build step exists; the site is the artifact):

```bash
curl -sS -o /dev/null -w "%{http_code} %{url_effective}\n" https://mgr-operations.github.io/robots.txt
curl -sS -o /dev/null -w "%{http_code} %{url_effective}\n" https://mgr-operations.github.io/sitemap.xml
curl -sS https://mgr-operations.github.io/services.html | grep -o '<link rel="canonical"[^>]*>'
curl -sS https://mgr-operations.github.io/ | grep -c 'application/ld+json'
```

Expected: `200` for both files, one matching canonical per page, one JSON-LD block on the home page.

Also verify manually: Schema Markup Validator (validator.schema.org), Rich Results Test, PageSpeed Insights (mobile), and URL Inspection in Search Console for each of the 4 URLs.

---

## Sources consulted

- Google Search documentation updates — FAQ rich results retired 7 May 2026: <https://developers.google.com/search/updates>
- Changes to HowTo and FAQ rich results (HowTo deprecated 13 Sep 2023): <https://developers.google.com/search/blog/2023/08/howto-faq-changes>
- Structured data gallery (which types Google supports): <https://developers.google.com/search/docs/appearance/structured-data/search-gallery>
- Organization structured data: <https://developers.google.com/search/docs/appearance/structured-data/organization>
- Canonical URLs: <https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls>
- Building sitemaps: <https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap>
- robots.txt (host-root requirement): <https://developers.google.com/crawling/docs/robots-txt/create-robots-txt>
- Core Web Vitals thresholds: <https://developers.google.com/search/docs/appearance/core-web-vitals> · <https://web.dev/articles/vitals>
- AI features and your website: <https://developers.google.com/search/docs/appearance/ai-features>
- Review snippet policy (self-serving reviews): <https://developers.google.com/search/docs/appearance/structured-data/review-snippet>
- Schema.org `ProfessionalService` deprecation: <https://schema.org/ProfessionalService>
- OpenAI crawlers (`OAI-SearchBot`, `GPTBot`): <https://developers.openai.com/api/docs/bots>
- Perplexity crawlers: <https://docs.perplexity.ai/docs/resources/perplexity-crawlers>
- Bing AI Performance report: <https://blogs.bing.com/webmaster/2026/2/Introducing-AI-Performance-in-Bing-Webmaster-Tools-Public-Preview/>
- GitHub Pages custom domains: <https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages>
- Ahrefs `llms.txt` study (97% of files received no requests in May 2026): <https://ahrefs.com/blog/what-is-llms-txt/>
- Ahrefs AI brand-visibility correlations (75k brands): <https://ahrefs.com/blog/ai-brand-visibility-correlations/>
- Competitor/SERP evidence for the query set in §3: ZenPilot (<https://www.zenpilot.com/clickup-consultant/>), Solution Integrators (<https://solutionintegrators.us/clickup-setup>), ClickUp partner directory (<https://clickup.com/partners>), ClickUp Verified Consultant guidance (<https://help.clickup.com/hc/en-us/articles/6311968068759-Become-ClickUp-Verified>)
