# Your Animus configuration — conductor pattern

This directory holds your Animus team and workflow definitions. The daemon
reads these files at startup; edits take effect on the next workflow run
or after `animus daemon restart`.

The conductor pattern adds a Product Owner (PO) agent on top of the standard
task-delivery loop. You write requirements; the PO decomposes them into ready
tasks; the daemon picks up ready tasks and ships them.

## Files

- `workflows/agents.yaml` — your AI team. Roles: PO, architect, implementer,
  reviewer, QA. Read it like a list of job descriptions.
- `workflows/requirements.yaml` — REQ-* -> tasks decomposition workflow run
  by the PO agent.
- `workflows/delivery.yaml` — the standard task-delivery workflow with real
  inline phases (subject-activate, implementation, review, qa, push-branch,
  create-pr).
- `workflows/schedules.yaml` — cron-driven workflow triggers. Ships with
  `work-planner` ENABLED to pick up ready tasks every 10 minutes.
- `workflows/triggers.yaml` — event-driven workflow triggers (webhooks, file
  watches). Empty by default.
- `workflows/custom.yaml` — top-level project config (default workflow ref,
  shell allowlist).

## Run your first requirement

```bash
animus requirements create \
  --title "Rate-limit the upload endpoint" \
  --priority must \
  --acceptance-criterion "Requests over the limit return HTTP 429" \
  --acceptance-criterion "Limits are configurable per-tenant"

animus workflow run --workflow requirements-workflow \
  --requirement-id REQ-001

# After the PO finishes decomposing, start the daemon:
animus daemon start --autonomous
```

The work-planner schedule (in `schedules.yaml`) will pick up each ready task
every 10 minutes and run it through `standard-workflow`.

## Edit your team

Open `workflows/agents.yaml`. Each agent is defined like a job description:

  - `description`   — what role this agent plays
  - `model`         — which LLM (sonnet / opus / haiku / gpt-5.x / gemini ...)
  - `tool`          — which CLI driver (claude / codex / gemini / opencode)
  - `skills`        — named behaviors composed into the system prompt at runtime
  - `tool_policy`   — which MCP tool ids this agent may call
  - `capabilities`  — feature flags the runtime uses for routing

The PO agent uses Opus by default for stronger decomposition quality. The
implementer and reviewer use Sonnet. The triager would use Haiku for cheap
queue evaluation. Adjust per your budget and quality needs.

## Edit a workflow

`requirements.yaml` defines two workflows: `requirements-workflow` (planning
only) and `requirements-execute-workflow` (planning + auto-enqueue).

`delivery.yaml` defines `standard-workflow` with six phases. You can:

  - reorder phases under `workflows[].phases`
  - add a new phase under `phases:` and reference it from a workflow
  - swap an `agent_id` to route work to a different team member
  - tighten gates with `decision_contract.min_confidence` / `max_risk`
  - flip `post_success.merge.auto_merge` to `true` for hands-off delivery

For a deeper dive, see `animus workflow list` and `animus doctor`.
