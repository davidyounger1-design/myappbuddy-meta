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
| Time Tracker (MAB SaaS) | `E:\MyAppBuddy\timetracker` (prod), `devtimetracker` (dev) — same repo | PHP-rendered, inline JS | PHP 8 + own MySQL DB | Live — timetracker.myappbuddy.com.au. **Split 2026-07-15** — no longer shares code with TSM's fork below; see that row and its own `CLAUDE.md` | Yes, 2026-07 |
| TSM Time Tracker | `E:\tsm_timetracker` — **separate repo** (github.com/davidyounger/tsm_timetracker, private, owner account `davidyounger` not `davidyounger1-design`) | PHP-rendered, inline JS, no build step, single file (`index.php`) | PHP 8 + own MySQL DB (TSM Hostinger account) | Live — theservicemanager.com/timetracker/. Forked from the MAB variant above 2026-07-15 with every MAB/subscription trace surgically removed (unrestricted features, no billing, no self-update — frozen, manually-deployed, diverges permanently). See its own `CLAUDE.md` for deploy steps and a known ternary-bug pattern to watch for elsewhere in the file | Yes, 2026-07-20 |
| Daily Brief | `E:\MyAppBuddy\dailybrief` (same repo) | PHP-rendered | PHP 8 + own MySQL DB, Gemini API | Live | Yes, 2026-07 |
| licencemanager (self-hosted MAB) | deployed copy of the hub, not a separate repo | same as hub | same as hub, own MySQL DB | Live — licencemanager.theservicemanager.com, TSM's own on-prem install of the MyAppBuddy hub | Yes, 2026-07 |
| Companion | `E:\companion` — **separate repo** | **not verified** — only its MAB integration (support tickets/ideas via the hub's public embed API) was touched. Lives in the shared **"MABApps" Supabase project** (ref `oprsmhyvihrahxpfvdih`) — NOT its own dedicated project; it's a tenant of that shared project (in `public` schema, predating the schema-per-app convention), distinct from the MAB hub's own separate project (`kqhyppacddcqkkovoklb`, name "MyAppBuddy") | Live, real subscribers | Partially — its use of the MAB hub API is verified, and the Supabase project ref/name confirmed via `supabase projects list` 2026-07-18; its own frontend/backend code is not verified |
| Haven | `E:\haven` — **separate repo** (github.com/davidyounger1-design/haven, private) | No-build in-browser JSX frontend (not yet ported, see below) / **Supabase-native backend** — own Postgres schema `haven` in the same shared "MABApps" project as Companion (`oprsmhyvihrahxpfvdih`); RLS + SECURITY DEFINER RPCs (no PHP backend), Supabase Auth, 3 edge functions for MAB subscription lifecycle + entitlements | Backend live — schema/RLS/RPCs/entitlements/webhook all confirmed working end-to-end. **Frontend not yet ported** (still the old NAS PIN-gated JSX) — not customer-facing yet | Yes, 2026-07-19 |
| TSM Subs | `E:\tsm_subs` | **not touched** this round | React+Vite+PHP/MySQL per earlier notes | Not re-verified — carry forward with caution |
| taskmanager / Task Planner | TSM web root `taskmanager/` | **not verified** | Deployed, but **no MAB subscription-platform wiring found** (no `MAB_SECRET_KEY`/`MAB_API_BASE` anywhere in it) — it is not currently a MAB-integrated product, whatever else it does | Partially — absence of MAB wiring confirmed 2026-07 |
| `myappbuddy` (retired), `myappbuddy-leave`, `leave_app`, other `Documents\Local_Claude\*` repos | `C:\Users\david\Documents\Local_Claude\` | — | Per that workspace's own `CLAUDE.md`: some are retired/superseded lineages (e.g. an earlier `myappbuddy` checkout retired in favour of `E:\MyAppBuddy`, an earlier single-tenant Leave build). **Do not assume these are the same code as `E:\MyAppBuddy\leave` etc. above** — check that workspace's own `CLAUDE.md` before touching anything there. Haven's own earlier NAS-based lineage (`E:\Haven update v1.7`, live at `192.168.1.203/haven/`) is superseded by `E:\haven` above, but the NAS install is still the only customer-usable version until Phase 4 (frontend port) lands. | Not re-verified this round |

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
  management via the CLI. `supabase projects list` (re-verified 2026-07-18)
  shows two projects: `MyAppBuddy` (ref `kqhyppacddcqkkovoklb`, the hub's own
  dedicated Postgres DB) and **`MABApps`** (ref `oprsmhyvihrahxpfvdih`) — a
  **shared** project used by multiple product apps, each in its own Postgres
  schema (not `public`, not a table prefix): Haven owns schema `haven`;
  Companion currently sits in `public` (predating this convention). Do not
  create a new Supabase project for a MABApps-family app without a specific
  reason — default to a new schema in the shared `MABApps` project instead.
  `E:\haven` is `supabase link`ed to `MABApps`; no other local checkout is
  linked yet. Runtime keys/connection strings live in each app's own config —
  PHP apps: `config.php` (gitignored; `config.example.php` is the committed
  template); Supabase-native apps (Haven): no local secrets file at all —
  Supabase secrets are managed entirely via `supabase secrets set`.
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
> `timetracker` / `leaveplanner` / `haven` (use whichever matches this app).
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

As of 2026-07-07: Companion and Time Tracker each still have their own
parallel plan/feature logic per step 4 above (not yet retired). Daily Brief
and Task Planner aren't MAB catalog apps at all yet, so this doesn't apply
to them until that's set up first.

**Haven fully adopted this 2026-07-19** — the first app to do so end-to-end.
5 candidate features published (`mab-features.json` + a standalone publish
script), ticked onto its one plan (`haven_starter`) in MAB Admin →
Entitlements, and gating confirmed working via both a real subscription's
`features` call AND the webhook (`subscription.created`/`subscription.canceled`)
keeping `haven.orgs.billing_status`/`plan` in sync. See `E:\haven`'s own
`README.md`/`SUPABASE.md` for the concrete implementation (RLS + SECURITY
DEFINER RPCs derive org_id server-side — a client can never pass its own
org_id, closing a class of bug worth checking for in any other app's
equivalent code).

## Quick-reference paths (primary Windows dev PC — adjust per machine)

Paths below are for the PC this table was written on. On a second PC, note
your own equivalents here (or in a short PC-specific addition) rather than
assuming these are universal — drive letters and folder layout may differ.

| Path | What |
|---|---|
| `E:\MyAppBuddy\` | MyAppBuddy hub + family apps (timetracker, devtimetracker, leave, dailybrief, etc.) |
| `E:\companion\` | Companion app (separate repo) |
| `E:\haven\` | Haven — NDIS budget planner (separate repo, Supabase-native backend) |
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


## Model selection for subagents

When spawning subagents for any part of a task, choose the model based on the nature of that specific piece of work rather than defaulting to one model for everything.

Use Opus for: ambiguous or open-ended problems with no clear spec, architectural or system-design decisions, tasks where a wrong call is expensive to unwind (security review, data migration logic, anything touching production data), multi-step reasoning where errors compound, or synthesizing conflicting information into a single judgment call.

Use Sonnet as the default for: standard implementation work with a clear spec (writing a function, wiring an API, fixing a described bug), most code review, refactors, and test writing — any subagent task well-scoped enough that a competent mid-level engineer could execute it from the instructions alone.

Use Haiku for: high-volume, low-ambiguity, repetitive work — the same transformation across many files, extracting structured data from text, bulk classification/tagging, formatting/linting passes, or simple lookups and summarization where speed and cost matter more than depth.

Decision rule: how much judgment does this task require, and how costly is a mistake? High judgment or high cost of error -> Opus. Clear spec, moderate complexity -> Sonnet. Repetitive, low-stakes, high-volume -> Haiku. When unsure between two tiers, prefer the cheaper one and escalate only if the output comes back inadequate.

When running independent subagents in parallel, mix models freely across the batch.

Before spawning a subagent where the right tier isn't obvious, state the model choice and reasoning in one line (e.g., "Opus - this migration plan has no existing spec and errors are costly to reverse").