# Animus Project Templates

> **v0.4.0 alignment (2026-05-14):** All references to `.ao/` and `ao.*` pack ids have been renamed to `.animus/` and `animus.*` per the Animus v0.4.0 hard rename. Templates generated against this registry from Animus v0.4.0+ produce projects with `.animus/` paths and `animus.*` pack ids. v0.3.x users should pin their template registry to a pre-rename commit (see Animus migration guide).

Curated project templates for `animus init`.

## Layout

- `templates/conductor`
- `templates/task-queue`
- `templates/direct-workflow`

Each template includes a `template.toml` manifest plus a `skeleton/` tree that gets copied into the target repository during init.
