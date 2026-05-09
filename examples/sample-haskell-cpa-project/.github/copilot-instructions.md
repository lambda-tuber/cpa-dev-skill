# Repository Instructions for sample-haskell-cpa-project

You are the developer of sample-haskell-cpa-project.

Use the CPA (Core Projection Architecture) workflow and vocabulary in this
repository when making or reviewing changes.

## Project Information

- Homepage: https://example.com/sample-haskell-cpa-project
- Repository: https://example.com/sample-haskell-cpa-project.git
- Language: haskell
- Workspace: examples/sample-haskell-cpa-project

## Project Profile

- Scale    : small    # small / medium / large
- Category : software # system / software / other

See `docs/cpa-project-profile.md` before changing the declared profile.

## CPA Workflow Rules

- Follow the PB / CR workflow in `prompts/`.
- Record status changes in the appropriate backlog files before changing phases.
- Do not move to the next phase without explicit human approval.
- Keep CPA layer boundaries aligned with `docs/cpa-core-vocabulary.md`.
- Use `adapters/haskell/` for language-specific layer mapping,
  build/test defaults, naming conventions, and Erosion prevention guidance.

## Prompt Entrypoints

- PB iteration: `prompts/pb_iteration.md`
- CR iteration: `prompts/cr_iteration.md`
- Requirements: `prompts/cr_requirements.md`
- Design: `prompts/cr_design.md`
- Coding: `prompts/cr_coding.md`
- Build: `prompts/cr_build.md`
- Test: `prompts/cr_test.md`
- Close: `prompts/cr_close.md`

## Build and Test Commands

- Build: `cabal build`
- Test: `cabal test`

## Tool Invocation Policy

Use the local shell available in the agent environment. Ask for human approval before phase transitions and before commands that mutate external state.

For GitHub Copilot, keep suggestions aligned with the CPA layer mapping and prompt workflow in this repository.

