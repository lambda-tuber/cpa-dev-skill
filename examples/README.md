# examples/

This directory contains sample initialization flows for CPA-compliant projects.

## Purpose

Examples demonstrate how to use `cpa-dev-skill` to initialize a new project
from scratch using `SKILL.md`, `templates/AGENTS.md`, and a selected adapter.
They serve as both documentation and validation that the skeleton and prompts
work end-to-end.

## CR-12 Validation Scenario

CR-12 validates a sample Haskell initialization flow:

- A worked example of filling `templates/AGENTS.md` for a Haskell project
- Optional tool-specific instructions for Claude Code and GitHub Copilot
- Initial `BACKLOG.md` and `STATUS.md` created from `templates/backlog/`
- Local copies of `docs/` and `prompts/` so generated instruction
  files resolve their repository-relative references
- Haskell adapter guidance merged into the root `AGENTS.md`
- Verification that the resulting structure enables CPA-compliant iteration

## Sample Project

The sample project is created under:

```
examples/sample-haskell-cpa-project/
```

## Validation Checks

The CR-12 validation checks:

- Generated instruction files have their core placeholders filled.
- `docs/` and `prompts/` exist in the sample project.
- No project-local `adapters/` directory is created by default.
- `prompts/pb_iteration.md` is available as the workflow entrypoint.
- `backlog/BACKLOG.md` and `backlog/STATUS.md` exist as the initial backlog.
- No PB folder or CR file exists until the PB Open or CR Open phase creates it.
- Legacy template names and stale CR-09/CR-10 placeholder status text have been
  removed from the skill documentation.
