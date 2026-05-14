# Animus Project Templates

> **v0.4.0 alignment (2026-05-14):** All references to `.ao/` and `ao.*` pack ids have been renamed to `.animus/` and `animus.*` per the Animus v0.4.0 hard rename. Templates generated against this registry from Animus v0.4.0+ produce projects with `.animus/` paths and `animus.*` pack ids. v0.3.x users should pin their template registry to a pre-rename commit (see Animus migration guide).

Curated project templates for `animus init`.

Each template ships a complete, learnable starter — a `.animus/` directory
with real inline agent definitions, real workflow phases, schedules,
triggers, and a README explaining the layout. New users can read their
freshly-initialized `.animus/` like a job description for their AI team
without spelunking through pack internals.

## Templates

### `templates/task-queue`

The simple default. For solo founders and small teams who already have
tasks (or generate them with `animus task create`) and want a clean
delivery loop with a reviewer gate.

What you get out of the box:

- **Agents:** `default` (implementer, Sonnet), `reviewer` (Sonnet),
  `triager` (Haiku for cheap queue evaluation).
- **Workflow:** `standard-workflow` — subject-activate -> implementation
  -> review -> push-branch -> create-pr. Reviewer rework verdicts route
  back to implementation.
- **Schedules:** none active by default.
- **Triggers:** none active by default.
- **Merge:** `auto_merge: false` — the user approves the PR on GitHub.

### `templates/conductor`

The richest template. For teams that decompose requirements into tasks via
a Product Owner agent and want autonomous delivery on top.

What you get out of the box:

- **Agents:** `po` (Opus, decomposes requirements -> tasks), `architect`
  (Opus, design notes for cross-cutting changes), `default` (Sonnet,
  implementer), `reviewer` (Sonnet), `qa` (Sonnet, validates acceptance
  criteria + adds test coverage).
- **Workflows:** `requirements-workflow` (PO plans), `requirements-execute-workflow`
  (PO plans + auto-enqueues), `standard-workflow` (subject-activate ->
  implementation -> review -> qa -> push -> create-pr).
- **Schedules:** `work-planner` ENABLED — runs the standard workflow
  against the next ready task every 10 minutes.
- **Triggers:** none active by default (commented GitHub-issue example
  shipped).
- **Merge:** `auto_merge: false` — human still approves the PR.

### `templates/direct-workflow`

The slim, human-driven variant. For users who want explicit control on
every step. No autonomous schedules, strong tool-policy denies, an
explicit `mode: manual` approval gate before push.

What you get out of the box:

- **Agents:** `default` (Sonnet, denies on `git.push`), `reviewer`
  (Sonnet, denies on all `git.*`).
- **Workflow:** `standard-workflow` — subject-activate -> implementation
  -> review -> **manual-approval** -> push -> create-pr (as draft).
- **Schedules:** empty.
- **Triggers:** empty.
- **Merge:** `auto_merge: false`, draft PR.

## Layout

Each template directory contains:

- `template.toml` — manifest consumed by `animus init` (template id, version,
  pack activations, daemon defaults, post-init `next_steps`).
- `skeleton/` — the tree copied verbatim into the target project's working
  directory. Always contains `.animus/README.md` and
  `.animus/workflows/{agents,delivery,schedules,triggers,custom}.yaml`.
  Conductor additionally ships `.animus/workflows/requirements.yaml`.
