# Your Animus configuration

This directory holds your Animus team and workflow definitions. The daemon
reads these files at startup; edits take effect on the next workflow run
or after `animus daemon restart`.

## Files

- `workflows/agents.yaml` — your AI team. Each agent has a role, model,
  skills, and tool policy. Read it like a list of job descriptions.
- `workflows/delivery.yaml` — the standard task-delivery workflow with real
  inline phases (subject-activate, implementation, review, push-branch, create-pr).
- `workflows/schedules.yaml` — cron-driven workflow triggers. Empty by default.
- `workflows/triggers.yaml` — event-driven workflow triggers (webhooks, file
  watches). Empty by default.
- `workflows/custom.yaml` — top-level project config (default workflow ref,
  shell allowlist).

## Run your first task

```bash
animus task create --title "Add rate limiting to /api/upload" \
  --task-type feature --priority high
animus daemon start --autonomous
```

Watch progress with `animus task list`, `animus daemon health`, and
`tail -f .animus/daemon.log`.

## Edit your team

Open `workflows/agents.yaml`. Each agent is defined like a job description:

  - `description`   — what role this agent plays
  - `model`         — which LLM (sonnet / opus / haiku / gpt-5.x / gemini ...)
  - `tool`          — which CLI driver runs the model (claude / codex / gemini / opencode)
  - `skills`        — named behaviors composed into the system prompt at runtime
  - `tool_policy`   — which MCP tool ids this agent may call
  - `capabilities`  — feature flags the runtime uses for routing decisions

Add a new agent by copying an existing block and adjusting. Reference it
from a phase in `delivery.yaml` via `agent_id: your-new-agent`.

## Edit a workflow

`delivery.yaml` defines one workflow (`standard-workflow`) and four phases.
You can:

  - reorder phases under `workflows[].phases`
  - add a new phase under `phases:` and reference it from a workflow
  - swap an `agent_id` to route work to a different team member
  - change `decision_contract.min_confidence` / `max_risk` to tighten gates
  - flip `post_success.merge.auto_merge` to `true` for hands-off delivery

For a deeper dive, see `animus workflow list` and `animus doctor`.
