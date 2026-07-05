# MyAppBuddy — Portfolio Context (Global)

This file loads into every Claude Code session on every PC it's set up on. It
gives account/tooling access and portfolio-wide conventions, so a session
doesn't need this re-explained. App-specific architecture belongs in that
app's own repo `CLAUDE.md`, not here.

**Accuracy rule for this file:** only write down what's been verified. If
something is a plan/target rather than current reality, label it as such
explicitly. A wrong "fact" here is worse than a missing one — it actively
misleads the next session instead of just leaving a gap.

## Setup on a new PC

Source of truth: this repo — https://github.com/davidyounger1-design/myappbuddy-meta

```
git clone https://github.com/davidyounger1-design/myappbuddy-meta.git ~/myappbuddy-meta
ln -s ~/myappbuddy-meta/CLAUDE.md ~/.claude/CLAUDE.md
```

(Windows without symlink permission — e.g. Developer Mode not enabled: `copy`
the file instead, and re-copy it after every edit. A symlink is preferred
because it can't silently drift out of date.)

Then, once per machine (these do **not** sync — each PC authenticates itself):

```
gh auth login
supabase login
```

Also register that machine's SSH public key in the relevant Hostinger hPanel
(see "Hostinger SSH access" below) — each PC needs its own keypair.

**To edit this file:** edit it in the `myappbuddy-meta` repo, commit, push,
then on the other PC: `git -C ~/myappbuddy-meta pull` (re-copy on Windows if
not symlinked).

## Who owns this

David Younger — sole developer of the MyAppBuddy framework and the apps
built on it. myappbuddy.com.au (Proware Pty Ltd). Also does unrelated work
for The Service Manager Pty Ltd (TSM) — some infrastructure (Hostinger
accounts, some app installs) is shared/adjacent between the two; don't
assume they're the same business.

## The MyAppBuddy hub (verified 2026-07-04)

- **Repo:** `E:\MyAppBuddy` on the primary dev PC — github.com/davidyounger1-design/myappbuddy.
  One repo holds the hub itself *and* several product apps as subdirectories
  (see table below) — not split into per-app repos today.
- **Backend:** single PHP 8 file, `api/index.php` (~200+ routes) + `api/lib/*.php`.
  Database is **Postgres, hosted on Supabase**, reached through a custom
  `db.php` + `pgTranslate()` layer that translates MySQL-dialect SQL written
  in the app code at query time — apps do not talk to Supabase directly or
  use the Supabase client SDK.
- **Frontend:** React 19 + Vite SPA (`web/src/`) — admin/owner/customer
  consoles. Embeddable widgets (`<myappbuddy-support>`, `<myappbuddy-ideas>`,
  `<myappbuddy-pricing>`) ship from `embed/` for other apps to drop in.
- **Deploy:** `deploy/deploy.sh {dev|live|leave|timetracker|licencemanager|...}`
  — builds, ships over SSH, runs migrations, health-checks. Self-hosted
  installs (e.g. a customer's own on-prem copy) instead get **signed update
  packages** via `deploy/publish.sh` (RSA-signed `.pkg.tar`, signature
  verified client-side before applying), not a direct deploy.
- **Hosting:** Hostinger, two separate accounts (see "Hostinger SSH access").
- Money is handled as integer minor units (cents) throughout — never floats.

## Apps in the portfolio

| App | Where | Frontend / backend | Status | Verified? |
|---|---|---|---|---|
| MyAppBuddy hub | `E:\MyAppBuddy` (repo root) | React+Vite / PHP 8 + Postgres (Supabase) | Live — myappbuddy.com.au | Yes, 2026-07 |
| Leave Planner | `E:\MyAppBuddy\leave` (same repo) | PHP-rendered, inline JS, no build step | PHP 8 + own MySQL DB | Live — two installs: myappbuddy.com.au/leave (sold to new customers) and leave.theservicemanager.com (in active use), same code | Yes, 2026-07 |
| Time Tracker | `E:\MyAppBuddy\timetracker` (same repo) | PHP-rendered, inline JS | PHP 8 + own MySQL DB | Live — timetracker.myappbuddy.com.au | Yes, 2026-07 |
| Daily Brief | `E:\MyAppBuddy\dailybrief` (same repo) | PHP-rendered | PHP 8 + own MySQL DB, Gemini API | Live | Yes, 2026-07 |
| licencemanager (self-hosted MAB) | deployed copy of the hub, not a separate repo | same as hub | same as hub, own MySQL DB | Live — licencemanager.theservicemanager.com, TSM's own on-prem install of the MyAppBuddy hub | Yes, 2026-07 |
| Companion | `E:\companion` — **separate repo** | **not verified** — only its MAB integration (support tickets/ideas via the hub's public embed API) was touched | Live, real subscribers | Partially — its use of the MAB hub API is verified; its own frontend/backend stack is not |
| TSM Subs | `E:\tsm_subs` | **not touched** this round | React+Vite+PHP/MySQL per earlier notes | Not re-verified — carry forward with caution |
| `myappbuddy-leave`, `leave_app`, `taskplanner`, other `Documents\Local_Claude\*` repos | `C:\Users\david\Documents\Local_Claude\` | — | Per that workspace's own `CLAUDE.md`: some are retired/superseded lineages (e.g. an earlier `myappbuddy` checkout, an earlier single-tenant Leave build). **Do not assume these are the same code as `E:\MyAppBuddy\leave` etc. above** — check that workspace's own `CLAUDE.md` before touching anything there. | Not re-verified this round |

**Known ambiguity to resolve:** there appear to be more than one historical
lineage for at least "Leave" and possibly "Daily Brief" (one under
`E:\MyAppBuddy`, another under `Documents\Local_Claude`). Confirm with David
which is current/live before assuming either is dead, and update this table
once resolved.

**Supabase migration:** the long-term direction is to move apps onto
Supabase as their backend. **Not yet true** for Leave Planner, Time Tracker,
or Daily Brief (each has its own MySQL DB today) — treat "it's on Supabase"
as a target, not current state, unless that app's own `CLAUDE.md` says
otherwise.

## Access & credentials (do NOT put secrets in this file)

- **GitHub:** `gh auth login` once per machine, then use `gh` directly —
  no tokens needed in prompts or files.
- **Supabase:** `supabase login` once per machine, for project/schema
  management via the CLI. Runtime keys/connection strings live in each app's
  own config — PHP apps: `config.php` (gitignored; `config.example.php` is
  the committed template); future Supabase-native apps: `.env` (gitignored,
  see that repo's `.env.example`).
- **Hostinger SSH** (needed for every PHP app above):
  - theservicemanager.com account (`u566554909`): `ssh -p 65002 -i ~/.ssh/hostinger_tsm u566554909@46.202.138.84`
  - myappbuddy.com.au account (`u404781907`): `ssh -p 65002 -i ~/.ssh/hostinger_myappbuddy u404781907@153.92.11.14`
  - Each PC needs its own keypair generated locally and its **public** half
    added in the relevant hPanel → Advanced → SSH Access. Never copy a
    private key between machines if a fresh keypair is just as easy.
- **Never** paste API keys, tokens, DB passwords, or the contents of any
  `config.php`/`.env` into chat or into this file. (History: several
  `config.php` files were committed to the MyAppBuddy repo with live
  secrets — all identified and rotated 2026-07-02. Old values in git history
  are dead, not a live risk, but don't add new ones.)

## Skills & session-scoped context

- Global skills live in `~/.claude/skills/`. Some projects (e.g.
  `Documents\Local_Claude`) also have **project-scoped** skills/hooks in
  their own `.claude/` that only apply when the working directory is under
  that tree — check both when looking for a skill, script, or hook.
- The Skill tool's live "available skills" list has been observed to
  under-report (e.g. surfacing only the most-recently-touched skill). For
  "what skills do I have" style questions, check `~/.claude/skills/` and the
  current project's `.claude/skills/` directly rather than trusting that
  list as exhaustive.
- Hooks/skills added or edited **during** a running session may not take
  effect immediately even though the directory is watched — retry once
  before concluding something is broken; if it still doesn't work, restart
  the session or reopen `/hooks`.

## Cross-app conventions

- **Commit style:** descriptive imperative subject line + a short "why"
  paragraph in the body. No enforced conventional-commits prefix today.
- **Deploy process:** `deploy/deploy.sh` and `deploy/publish.sh` (see the
  MyAppBuddy repo's own docs) — not Vercel/Netlify/a CI pipeline.
- **Secrets:** never git-tracked, ever. Use a `*.example.php` / `.env.example`
  template instead and gitignore the real file.
- **Before building or deploying:** `git pull` first in whichever repo
  you're in. Resolve merge conflicts properly — never just overwrite.

## Known cross-cutting work

- **Companion ↔ MAB hub integration (fixed 2026-07-04):** Companion's
  support-ticket/idea embed widgets pass `app_ref = the user's email`, but
  Companion had no MAB secret API key, so it could never call
  `PUT /v1/link/subscriptions/:id` to register `app_ref` on its
  subscriptions — meaning idea voting/commenting and ticket replies (which
  require an active-subscription match) always failed for real subscribers,
  even though viewing/submitting always worked. Fixed hub-side with a
  one-time backfill + a newly-minted secret key for `app_id=companion`.
  **Still needed in Companion's own repo:** store `MAB_SECRET_KEY` and call
  `PUT /v1/link/subscriptions/:id {"orgId": "<user's email>"}` after each
  checkout so new subscriptions register `app_ref` automatically. See the
  Companion session's own history for the full handoff instructions.
- Portfolio-wide move to Supabase: aspirational — no product app besides the
  MAB hub's own DB currently runs on Supabase.
