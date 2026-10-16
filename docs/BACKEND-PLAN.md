# MGR Website — Backend Plan

Status: **planned, not built yet.** The site is currently static on GitHub Pages
(`mgr-operations.github.io`). This document defines the backend we will add when
the site is ready to become a "real" hosted product, plus what already exists in
the frontend to connect.

---

## 1. Current state

| Layer | Status |
|---|---|
| Frontend | Static HTML/CSS/JS — `index.html`, `services.html`, `plans.html`, `support.html`, `styles.css` |
| Hosting | GitHub Pages (static files only, no server code) |
| Backend | None yet — support form runs in "fallback mode" (see §4) |

GitHub Pages can never run the backend itself: it serves static files only.
The backend therefore runs as a **separate small API** that the pages call.

---

## 2. Goals (when we build it)

1. Receive **support / contact / quote requests** from the site forms.
2. **Store** submissions in a database so none are lost, with a status you can track (`new → replied → closed`).
3. **Notify** you (email) on every new submission.
4. Spam protection (honeypot + rate limiting).
5. Later phases: client login, client portal, plan checkout, content API.

---

## 3. Architecture options

| Option | How it works | Pros | Cons |
|---|---|---|---|
| **A. Serverless function + SQL DB (recommended)** | Small API (e.g. a Cloudflare Worker) with a SQLite-class DB (e.g. D1), on an `api.` subdomain | Real database, full control, no cold-start pain, can grow into client portal | Needs a hosting account; a few hours of setup |
| **B. Platform functions (Vercel / Netlify / Render)** | Same API code deployed through a git-connected platform | Easy deploys from this repo | Ties hosting of front+back to one vendor; heavier than needed for a form |
| **C. Third-party form endpoint** | Form POSTs to a hosted form service that stores/emails submissions | Almost zero code, works today | Per-submission cost, less control, data lives in a third party |

**Recommendation:** Option **A** when the time comes — it keeps the site static
on GitHub Pages (fast, free, unchanged) and adds one small API service.
Pick the concrete provider and any transactional-email vendor at implementation
time; do not commit before comparing current pricing.

---

## 4. API design (Option A)

Base URL: `https://api.<your-domain>/` (or a platform URL during development).

### `POST /api/support`
Creates a support/contact/quote request.

Request (`Content-Type: application/json`):

```json
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "company": "Acme Ltd",
  "topic": "New project request",
  "message": "We need to automate our payment request process…",
  "page": "/support.html",
  "website": ""          // honeypot — must be empty
}
```

Validation & rules:
- `name` 1–120 chars, `email` valid format, `topic` one of the form options,
  `message` 10–5,000 chars — otherwise `400` with a field-errors object.
- `website` non-empty ⇒ silently accept (`200`) but discard — bots must not learn anything.
- Rate limit: e.g. max 5 submissions / IP / 10 min ⇒ `429`.
- CORS: only the site origin(s) — `https://mgr-operations.github.io` (+ local dev origin).
- Store `user_agent`, `ip` (or a hash), `created_at`, `status='new'`.

Responses:
- `201 { "ok": true, "id": "…" }` on success
- `400` validation failed, `429` rate limited, `500` server error

### `GET /api/health`
`200 { "ok": true, "version": "1.0.0" }` — used for uptime checks.

### Later
- `GET /api/plans` — serve pricing from the DB instead of `plans.html` markup (only worth it if prices change often).
- `GET /api/submissions` (authenticated, admin-only) — inbox view of requests.
- `PATCH /api/submissions/:id` — change status.

---

## 5. Database schema (SQL)

```sql
CREATE TABLE submissions (
  id          TEXT PRIMARY KEY,          -- e.g. UUID
  kind        TEXT NOT NULL,             -- 'support' | 'contact' | 'quote'
  name        TEXT NOT NULL,
  email       TEXT NOT NULL,
  company     TEXT,
  topic       TEXT NOT NULL,
  message     TEXT NOT NULL,
  page        TEXT,                      -- which page it came from
  status      TEXT NOT NULL DEFAULT 'new',   -- new | replied | closed
  ip_hash     TEXT,
  user_agent  TEXT,
  created_at  TEXT NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX idx_submissions_status_created
  ON submissions (status, created_at DESC);
```

Notification: after a successful insert, trigger an email via a transactional
email provider (choose at build time) to your inbox. Emails must never contain
credentials; the DB contains personal data — keep backups and define a retention
policy (e.g. purge rows older than 24 months).

---

## 6. Frontend wiring (already in place)

The support form in `support.html` supports **three modes**, configured at the
bottom of that file:

```js
const API_ENDPOINT  = ""; // 1) set once the backend is deployed → form POSTs JSON
const SUPPORT_EMAIL = ""; // 2) set an email → form opens the visitor's mail app
```

| Config | What the visitor experiences |
|---|---|
| `API_ENDPOINT` set | Form submits to the API, shows "request received" confirmation |
| `SUPPORT_EMAIL` set | Visitor's mail client opens with the message pre-filled |
| Neither (today) | Honest fallback: book-a-call / LinkedIn links — never a fake success |

**Activation step when the backend is live:** set `API_ENDPOINT` in
`support.html` to the deployed URL and redeploy — no other frontend changes.

Other prepared items:
- Honeypot field `website` is already rendered (hidden) in the form and checked client-side; the API must check it again server-side.
- All contact entry points (nav, footer, CTAs, form fallback) already exist.

---

## 7. Build & deployment checklist (for later)

1. Choose provider + email service; compare current pricing/limits.
2. Create the project and the database; run the schema (§5).
3. Implement `POST /api/support`, `GET /api/health` with validation, rate limiting, CORS.
4. Test locally first (emulator/miniflare/wrangler dev), including: valid submit, missing fields, honeypot, rate limit, foreign-origin CORS.
5. Deploy to a staging URL; set `API_ENDPOINT` in `support.html`; verify end-to-end.
6. Set API secrets as **environment variables / secret manager** — never in this repo, never in frontend code.
7. Add uptime monitoring + error alerts on the API.
8. Move to a custom domain if desired (`www` + `api`), keeping GitHub Pages for the frontend.

---

## 8. Security notes

- Never store API keys or SMTP passwords in this repository or in frontend JS.
- Validate and sanitize server-side; client-side validation is convenience only.
- Rate-limit per IP; keep the honeypot; consider a CAPTCHA only if spam appears.
- Personal data (names/emails/messages) is stored — restrict access to the DB,
  log access, and honor deletion requests.
- Serve the API over HTTPS only; lock CORS to the real site origin.
