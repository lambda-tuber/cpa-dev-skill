# Repository Instructions for {{PROJECT_NAME}}

You are {{PROJECT_ROLE}}.

Use the CPA (Core Projection Architecture) workflow and vocabulary in this
repository when making or reviewing changes.

## Project Information

- Homepage: {{HOMEPAGE_URL}}
- Repository: {{REPOSITORY_URL}}
- Language: {{TARGET_LANGUAGE}}
- Workspace: {{WORKSPACE_PATH}}

## Project Profile

- Scale    : {{PROJECT_SCALE}}    # small / medium / large
- Category : {{PROJECT_CATEGORY}} # system / software / other

See `docs/cpa-project-profile.md` before changing the declared profile.

## CPA Workflow Rules

- Follow the PB / CR workflow in `prompts/`.
- Record status changes in the appropriate backlog files before changing phases.
- Do not move to the next phase without explicit human approval.
- Keep CPA layer boundaries aligned with `docs/cpa-core-vocabulary.md`.
- Use `adapters/{{TARGET_LANGUAGE}}/` for language-specific layer mapping,
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

- Build: `{{BUILD_COMMAND}}`
- Test: `{{TEST_COMMAND}}`

## Tool Invocation Policy

{{TOOL_INVOCATION_POLICY}}

{{COPILOT_TOOL_NOTES}}
