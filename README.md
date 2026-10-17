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
  (`API_ENDPOINT`, `SUPPORT_EMAIL`). Until one is set, the form shows the
  book-a-call / LinkedIn fallback instead of a fake success.

## Backend (planned, not deployed)

GitHub Pages serves static files only, so the future API lives elsewhere.
The full plan — architecture options, endpoint specs, database schema, form
wiring and the deployment checklist — is in
[`docs/BACKEND-PLAN.md`](docs/BACKEND-PLAN.md).

## SEO (planned, not deployed)

The site currently has no canonical tags, structured data, `robots.txt` or
`sitemap.xml`. The audit, keyword targets, phases and ready-to-paste snippets
are in [`docs/SEO-PLAN.md`](docs/SEO-PLAN.md) — start with Phase 0 there.

## Local preview

```bash
python -m http.server 8080
# open http://localhost:8080
```
