# Continuous Documentation

Cursor plugin that keeps the repository `readme.md` current by mining conversation transcripts for documentation-worthy changes, capturing intent and reasoning behind decisions.

## How it works

A `stop` hook tracks conversation cadence. When enough turns and time have passed, it triggers the `continuous-documentation` skill.

The skill combines two sources of truth:

- **Git history** — what changed in the code (`git log`, `git diff` since the last indexed commit).
- **Conversation transcripts** — why it changed (stated reasoning, rejected alternatives, constraints).

It then updates `readme.md` following project-type-specific structure guidelines (Service, Monolith, UI, or Package) and strips AI filler language via an embedded slop filter.

Processing is incremental — only new commits and changed transcripts are evaluated on each run.

## Prerequisites

The hook script runs with [Bun](https://bun.sh/). Ensure `bun` is available on `PATH`.

## State files

| File | Purpose |
|------|---------|
| `.cursor/hooks/state/continuous-documentation.json` | Cadence state (turn count, last run time, transcript mtime) |
| `.cursor/hooks/state/continuous-documentation-index.json` | Incremental index (last commit SHA, processed transcript mtimes) |

Both are workspace-local and excluded from version control.

## Trigger cadence

| Setting | Default | Trial mode |
|---------|---------|------------|
| Minimum turns | 10 | 3 |
| Minimum minutes | 120 | 15 |
| Trial duration | — | 24 hours |

All conditions must be met simultaneously. Transcript mtime must also advance since the previous run.

## Configuration

All settings are optional environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `CONTINUOUS_DOCUMENTATION_MIN_TURNS` | Minimum completed turns before triggering | 10 |
| `CONTINUOUS_DOCUMENTATION_MIN_MINUTES` | Minimum minutes between runs | 120 |
| `CONTINUOUS_DOCUMENTATION_TRIAL_MODE` | Enable reduced thresholds for a trial window | false |
| `CONTINUOUS_DOCUMENTATION_TRIAL_MIN_TURNS` | Minimum turns in trial mode | 3 |
| `CONTINUOUS_DOCUMENTATION_TRIAL_MIN_MINUTES` | Minimum minutes in trial mode | 15 |
| `CONTINUOUS_DOCUMENTATION_TRIAL_DURATION_MINUTES` | Trial window length in minutes | 1440 |

## License

MIT
