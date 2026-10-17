# MGR Solutions — Website

Static marketing site for **MGR — Management · Growth · Results**
(ClickUp & Operations Solutions), published on GitHub Pages:
<https://mgr-operations.github.io/>

## Pages

| File | Purpose |
|---|---|
| `index.html` | Home — hero, credentials, problems, services overview, plans teaser, case study, process, about, contact |
| `services.html` | Detailed breakdown of all 8 services + how we work |
| `plans.html` | Implementation packages (Starter / Professional / Enterprise), comparison table, monthly retainers, pricing FAQ |
| `support.html` | Help categories, support request form, support FAQ |
| `styles.css` | Shared stylesheet used by every page (single source of truth) |

Shared pieces on every page: sticky navigation (with a CSS-only mobile menu),
footer with page links, and a `#contact` CTA section.

## Editing notes

- **Styling:** change `styles.css` only — do not re-add inline `<style>` blocks.
- **Navigation:** the nav/footer markup is duplicated per page; when adding a
  link, update all four files (`index`, `services`, `plans`, `support`).
- **Prices:** the "From $…" figures on `plans.html` (and the teaser on the home
  page) are starting points — adjust them there when your quote structure changes.
- **Support form:** configuration lives at the bottom of `support.html`
  (`API_ENDPOINT`, `SUPPORT_EMAIL`). `SUPPORT_EMAIL` is set to
  `support.mgr.solutions@gmail.com`, so submissions open the visitor's mail
  app pre-filled. If neither is set, the form shows the book-a-call /
  LinkedIn fallback instead of a fake success.
- **Email contact:** every "Let's talk" section has a `Contact Us` button
  linking to the same address (with a page-specific subject line).

## Backend (planned, not deployed)

GitHub Pages serves static files only, so the future API lives elsewhere.
The full plan — architecture options, endpoint specs, database schema, form
wiring and the deployment checklist — is in
[`docs/BACKEND-PLAN.md`](docs/BACKEND-PLAN.md).

## SEO

Phase 0 is live: every page has a self-referential canonical and full social
metadata, the home page carries the `Organization` structured data (inner pages
carry `BreadcrumbList`, `services.html` describes its 8 services), and
`robots.txt` + `sitemap.xml` are published at the root. The hero logo is served
as `logo-hero.webp` (15.8 KB, down from a 1.5 MB PNG).

Remaining work — Search Console/Bing verification, content pages, off-site
profiles — is tracked in [`docs/SEO-PLAN.md`](docs/SEO-PLAN.md).

When adding a page, add its canonical, its `sitemap.xml` entry, and a
`BreadcrumbList` block so the patterns stay consistent.

## Local preview

```bash
python -m http.server 8080
# open http://localhost:8080
```
