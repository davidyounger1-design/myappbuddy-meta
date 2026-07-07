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

See [SETUP.md](SETUP.md) in this repo for the new-machine bootstrap steps
(clone, symlink, `gh auth login`, `supabase login`, SSH keypair registration).

## Who owns this

David Younger — sole developer of the MyAppBuddy framework and the apps
built on it. myappbuddy.com.au (Proware Pty Ltd). Also does unrelated work
for The Service Manager Pty Ltd (TSM) — some infrastructure (Hostinger
accounts, some app installs) is shared/adjacent between the two; don't
assume they're the same business.

## The MyAppBuddy hub

App-specific architecture now lives in the hub's own repo —
[`E:\MyAppBuddy\CLAUDE.md`](E:\MyAppBuddy\CLAUDE.md) (verified 2026-07-04) —
per this file's own rule that app-specific detail belongs there, not here.
See that file for stack, deploy process, and structure.

## Apps in the portfolio

| App | Where | Frontend / backend | Status | Verified? |
|---|---|---|---|---|
| MyAppBuddy hub | `E:\MyAppBuddy` (repo root) | React+Vite / PHP 8 + Postgres (Supabase) | Live — myappbuddy.com.au | Yes, 2026-07 |
| Leave Planner | `E:\MyAppBuddy\leave` (same repo) | PHP-rendered, inline JS, no build step | PHP 8 + own MySQL DB | Live — two installs: myappbuddy.com.au/leave (sold to new customers) and leave.theservicemanager.com (in active use), same code | Yes, 2026-07 |
| Time Tracker | `E:\MyAppBuddy\timetracker` (prod), `devtimetracker` (dev) — same repo | PHP-rendered, inline JS | PHP 8 + own MySQL DB | Live — timetracker.myappbuddy.com.au | Yes, 2026-07 |
| Daily Brief | `E:\MyAppBuddy\dailybrief` (same repo) | PHP-rendered | PHP 8 + own MySQL DB, Gemini API | Live | Yes, 2026-07 |
| licencemanager (self-hosted MAB) | deployed copy of the hub, not a separate repo | same as hub | same as hub, own MySQL DB | Live — licencemanager.theservicemanager.com, TSM's own on-prem install of the MyAppBuddy hub | Yes, 2026-07 |
| Companion | `E:\companion` — **separate repo** | **not verified** — only its MAB integration (support tickets/ideas via the hub's public embed API) was touched. Has its **own Supabase project** (`companion`, ref `oprsmhyvihrahxpfvdih`) — separate from the MAB hub's project (`kqhyppacddcqkkovoklb`); presumably its own app data lives there, distinct from the shared support/ideas data that lives in the MAB hub's Postgres | Live, real subscribers | Partially — its use of the MAB hub API is verified, and its Supabase project's existence/ref is confirmed via `supabase projects list`; its own frontend/backend code is not verified |
| TSM Subs | `E:\tsm_subs` | **not touched** this round | React+Vite+PHP/MySQL per earlier notes | Not re-verified — carry forward with caution |
| taskmanager / Task Planner | TSM web root `taskmanager/` | **not verified** | Deployed, but **no MAB subscription-platform wiring found** (no `MAB_SECRET_KEY`/`MAB_API_BASE` anywhere in it) — it is not currently a MAB-integrated product, whatever else it does | Partially — absence of MAB wiring confirmed 2026-07 |
| `myappbuddy` (retired), `myappbuddy-leave`, `leave_app`, other `Documents\Local_Claude\*` repos | `C:\Users\david\Documents\Local_Claude\` | — | Per that workspace's own `CLAUDE.md`: some are retired/superseded lineages (e.g. an earlier `myappbuddy` checkout retired in favour of `E:\MyAppBuddy`, an earlier single-tenant Leave build). **Do not assume these are the same code as `E:\MyAppBuddy\leave` etc. above** — check that workspace's own `CLAUDE.md` before touching anything there. | Not re-verified this round |

**Known ambiguity to resolve:** there appear to be more than one historical
lineage for at least "Leave" (and possibly others) — one under
`E:\MyAppBuddy`, another under `Documents\Local_Claude`. Confirm with David
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
  management via the CLI. `supabase projects list` (verified 2026-07-05)
  shows two projects: `MyAppBuddy` (ref `kqhyppacddcqkkovoklb`, the hub's own
  Postgres DB) and `companion` (ref `oprsmhyvihrahxpfvdih`, Companion's own
  project). Neither is `supabase link`ed in any local checkout yet. Runtime
  keys/connection strings live in each app's own config — PHP apps:
  `config.php` (gitignored; `config.example.php` is
  the committed template); future Supabase-native apps: `.env` (gitignored,
  see that repo's `.env.example`).
- **Hostinger SSH:**
  - **theservicemanager.com** account (`u566554909`): `ssh -p 65002 -i ~/.ssh/hostinger_tsm u566554909@46.202.138.84`
    Web root: `domains/theservicemanager.com/public_html/`. Subdirectories:
    `leave/` (Leave Planner, PHP), `taskmanager/` (Task Planner — no MAB
    wiring, see table above), `timetracker/`, `subs/`, `tsmdl/`, `feedback/`,
    `tsmpluspoc/`, plus a WordPress install at the web root. This plan also
    supports Node.js apps (hPanel → Advanced → Node.js setup) if a future
    app needs that instead of PHP.
  - **myappbuddy.com.au** account (`u404781907`): `ssh -p 65002 -i ~/.ssh/hostinger_myappbuddy u404781907@153.92.11.14`
  - Each PC needs its own keypair generated locally and its **public** half
    added in the relevant hPanel → Advanced → SSH Access. Never copy a
    private key between machines if a fresh keypair is just as easy.
  - **These are LIVE/production apps — edits take effect immediately.** Back
    up any file (copy to `.bak`) before editing directly on a server.
- **Never** paste API keys, tokens, DB passwords, or the contents of any
  `config.php`/`.env` into chat or into this file. (History: several
  `config.php` files were committed to the MyAppBuddy repo with live
  secrets — all identified and rotated 2026-07-02. Old values in git history
  are dead, not a live risk, but don't add new ones.)

## Skills, hooks, and multi-workspace `.claude/` directories

- Global skills live in `~/.claude/skills/`. Some workspaces (e.g.
  `C:\Users\david\Documents\Local_Claude`, a separate multi-project folder
  holding MyAppBuddy/Leave/TaskPlanner/DailyBrief/TSMPlus-family work) have
  their **own** `.claude/` with project-scoped skills, scripts, and hooks
  that only apply when the working directory is under that tree — check
  both locations when looking for a skill, config file, or script (e.g. an
  `explain` skill exists in both places, as distinct copies).
- The Skill tool's live "available skills" list has been observed to
  under-report (e.g. surfacing only the single most-recently-touched skill)
  instead of the full set. For "list my skills" style requests, verify
  directly against `~/.claude/skills/` and the current project's
  `.claude/skills/` instead of trusting that list as exhaustive.
- Hooks/skills added or edited **during** a running session may not take
  effect immediately even though the directory is already being watched —
  observed cases: a brand-new hook fired live but a later edit to that same
  hook's command wasn't picked up until restart; a newly created skill
  failed as "unknown command" on first invocation then worked on immediate
  retry. Verify once after adding/editing; retry once if it fails; if it
  still doesn't work, restart the session or reopen `/hooks` rather than
  deep-diagnosing further — there's no way to force a reload from within a
  session.

## Session behavior norms

- **Don't execute example content as a literal task.** If a message reads as
  a concrete, actionable request but immediately follows an interrupted
  meta-request (e.g. "turn this into a reusable skill/template" cut off
  before the example was pasted), check whether the new message is actually
  the example payload for that meta-request before acting on it literally.
- Verify facts with a tool before stating them as true — don't theorize or
  guess, especially about live/production state. If two sources of context
  disagree (e.g. this file vs. what a live system actually shows), say so
  and ask, rather than picking one silently.

## Cross-app conventions

- **Commit style:** descriptive imperative subject line + a short "why"
  paragraph in the body. No enforced conventional-commits prefix today.
- **Deploy process:** `deploy/deploy.sh` and `deploy/publish.sh` (see the
  MyAppBuddy repo's own docs) — not Vercel/Netlify/a CI pipeline.
- **Secrets:** never git-tracked, ever. Use a `*.example.php` / `.env.example`
  template instead and gitignore the real file.
- **Before building or deploying:** `git pull` first in whichever repo
  you're in. Resolve merge conflicts properly — never just overwrite.

## MAB feature-restriction framework — required for every app

Formalized 2026-07-07 in the MyAppBuddy hub (`E:\MyAppBuddy` —
see its own `CLAUDE.md` for the hub-side schema/API detail). Any Claude Code
session working in an app that plugs into MyAppBuddy (Companion, Time
Tracker, Leave Planner, and any future MAB-integrated app) should apply this
when the app adds a feature worth restricting by plan:

> **Align this app with MyAppBuddy's plan-feature-restriction framework**
>
> MyAppBuddy (the hub at myappbuddy.com.au) is now the single source of
> truth for "which plan includes which feature." This app should never
> maintain its own separate copy of plan/feature/entitlement data — it
> declares candidate features to the hub, and asks the hub what's actually
> included before restricting anything. Your `app_id` is `companion` /
> `timetracker` / `leaveplanner` (use whichever matches this app).
>
> 1. **Create a manifest** — a small file at the repo root (e.g.
>    `mab-features.json`): an array of `{key, name}` for features worth
>    per-plan control. Not every function — only things significant enough
>    that "which plan gets this" is a real product decision (new premium
>    capability, usage limit, etc.). This is a judgement call made when the
>    feature is built, reviewed like any other code.
> 2. **Publish it on deploy** — add a step to your deploy script that POSTs
>    the manifest: `POST https://myappbuddy.com.au/api/v1/apps/<app_id>/features/publish`,
>    body `{"features": [{"key": "...", "name": "..."}]}`, header
>    `Authorization: Bearer <this app's secret key>` (get one from MAB
>    Admin → Developers if this app doesn't have one yet). Safe to run on
>    every deploy — an already-known key is a no-op. A genuinely new key
>    gets created but starts **included in no plan** — a pill/badge shows
>    up in the MAB admin console for review and plan assignment. Don't
>    expect a newly-published feature to be live for any customer until
>    that review happens.
> 3. **Gate behavior by calling the hub, not local logic** —
>    `GET /v1/subscriptions/:id/features` (publishable key), or for a
>    self-hosted install, read the `features` claim already in the signed
>    license. Treat a missing key as **NOT included** (fail closed) — never
>    treat "hub hasn't decided yet" as "allowed."
> 4. **Find and retire any parallel plan/feature logic already in this
>    app** — a local plans table, a hardcoded pricing/feature array, a
>    hand-coded limits function, anything deciding entitlements outside the
>    hub. There should be exactly one source of truth.
> 5. **Don't touch pricing or plan definitions** (name, price, which plans
>    exist) — that stays exclusively in MAB Admin → Plans & pricing /
>    Entitlements. This app only proposes candidate features and reads the
>    resulting decision.

As of 2026-07-07: no app has actually adopted this yet (mechanism exists,
nothing calls it) — Companion and Time Tracker each still have their own
parallel plan/feature logic per step 4 above. Daily Brief and Task Planner
aren't MAB catalog apps at all yet, so this doesn't apply to them until
that's set up first.

## Quick-reference paths (primary Windows dev PC — adjust per machine)

Paths below are for the PC this table was written on. On a second PC, note
your own equivalents here (or in a short PC-specific addition) rather than
assuming these are universal — drive letters and folder layout may differ.

| Path | What |
|---|---|
| `E:\MyAppBuddy\` | MyAppBuddy hub + family apps (timetracker, devtimetracker, leave, dailybrief, etc.) |
| `E:\companion\` | Companion app (separate repo) |
| `E:\tsm_subs\` | TSM Subs (separate repo) |
| `C:\Users\david\Documents\Local_Claude\` | Multi-project workspace (older/parallel MyAppBuddy, Leave, TaskPlanner, DailyBrief, TSMPlus lineage — see "known ambiguity" above) |
| `E:\task manager\` | Task Manager app |
| `E:\Leave\` | Leave app (yet another path — reconcile against `E:\MyAppBuddy\leave` and `Documents\Local_Claude` before assuming which is current) |
| `E:\tsm\` | TSM-related work |
| `C:\Users\david` | Home — global Claude config only, not a project root |



# Global Instructions

These are generic Claude Code behavior rules, not MyAppBuddy-specific —
split into their own file so this file's scope stays honest to its title.

@global-instructions.md
