# Your Animus configuration — direct-workflow pattern

This directory holds your Animus team and workflow definitions. The daemon
reads these files at startup; edits take effect on the next workflow run
or after `animus daemon restart`.

The direct-workflow pattern is built for projects that want **explicit
human approval at each step**. The team is minimal (one implementer, one
reviewer); the delivery workflow includes a `mode: manual` approval gate
between review and push; auto-merge is OFF; schedules and triggers are
empty by default.

## Files

- `workflows/agents.yaml` — your AI team. Two agents: `default` (implementer)
  and `reviewer`. Both have strict tool-policy denies on git push and
  destructive operations.
- `workflows/delivery.yaml` — the standard task-delivery workflow with
  inline phases (subject-activate, implementation, review, **manual-approval**,
  push-branch, create-pr-as-draft).
- `workflows/schedules.yaml` — empty by default. You run workflows manually.
- `workflows/triggers.yaml` — empty by default.
- `workflows/custom.yaml` — top-level project config (default workflow ref,
  shell allowlist).

## Run your first task

```bash
animus task create --title "Add rate limiting to /api/upload" \
  --task-type feature --priority high

# Run the workflow synchronously — it will pause at the manual-approval phase
animus workflow run --task-id TASK-001 --sync
```

When the workflow pauses at `manual-approval`, inspect `git diff` and the
reviewer's verdict, then approve with:

```bash
animus workflow phase approve --workflow-id WF-001 --phase manual-approval
```

## Edit your team

Open `workflows/agents.yaml`. Each agent is defined like a job description:

  - `description`   — what role this agent plays
  - `model`         — which LLM (sonnet / opus / haiku / gpt-5.x / gemini ...)
  - `tool`          — which CLI driver (claude / codex / gemini / opencode)
  - `skills`        — named behaviors composed into the system prompt at runtime
  - `tool_policy`   — which MCP tool ids this agent may call (note the deny
                      list includes `git.push` to enforce the human gate)
  - `capabilities`  — feature flags the runtime uses for routing

## Edit a workflow

`delivery.yaml` defines `standard-workflow` with six phases. You can:

  - reorder phases under `workflows[].phases`
  - add a new phase under `phases:` and reference it from a workflow
  - swap an `agent_id` to route work to a different team member
  - relax gates with `decision_contract.min_confidence` / `max_risk` if
    you want less pause-and-ask behavior
  - flip `post_success.merge.auto_merge` to `true` if you decide you want
    to grant the daemon merge authority

For a deeper dive, see `animus workflow list` and `animus doctor`.
