# Continuous Documentation

Cursor plugin that keeps the repository `readme.md` current by mining conversation transcripts for documentation-worthy changes.

## How it works

A `stop` hook tracks conversation cadence. When thresholds are met, it triggers the `continuous-documentation` skill, which:

1. Reads the current `readme.md`.
2. Processes new or changed transcript files (incremental — skips already-processed ones).
3. Extracts documentation-worthy changes: new features, design decisions, architectural shifts, business logic.
4. Captures the *why* behind changes from conversation context.
5. Updates the README following embedded structure guidelines (Service, Monolith, UI, or Package).
6. Strips AI filler language before writing.

## State files

The hook writes cadence state to `.cursor/hooks/state/continuous-documentation.json` in the workspace.
The skill writes an incremental transcript index to `.cursor/hooks/state/continuous-documentation-index.json`.

## Trigger cadence

| Setting | Default | Trial mode |
|---------|---------|------------|
| Minimum turns | 10 | 3 |
| Minimum minutes | 120 | 15 |
| Trial duration | — | 24 hours |

Transcript mtime must advance since the previous run.

## Env overrides

- `CONTINUOUS_DOCUMENTATION_MIN_TURNS`
- `CONTINUOUS_DOCUMENTATION_MIN_MINUTES`
- `CONTINUOUS_DOCUMENTATION_TRIAL_MODE`
- `CONTINUOUS_DOCUMENTATION_TRIAL_MIN_TURNS`
- `CONTINUOUS_DOCUMENTATION_TRIAL_MIN_MINUTES`
- `CONTINUOUS_DOCUMENTATION_TRIAL_DURATION_MINUTES`

## License

MIT
