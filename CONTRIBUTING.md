# Contributing

Thank you for your interest in contributing.

## Ground Rules

- Read `AGENTS.md` and `spec.md` before making changes.
- Keep documentation aligned with the current implementation.
- Do not add compatibility shims, legacy aliases, or historical adapter layers unless the project owner explicitly requests them.
- Do not describe unimplemented features as current product facts.
- Keep changes scoped to the current task.

## Development Flow

1. Understand the current repository facts.
2. Update or create `plan.md` for non-trivial work.
3. Implement the change.
4. Add or update tests that protect the core behavior.
5. Run the project verification command.
6. Update documentation if product facts changed.

## Verification

Run the project verification command before submitting changes:

```text
<verification command>
```

## Commits and Pushes

Commits and pushes should only happen when the project owner explicitly requests them.
