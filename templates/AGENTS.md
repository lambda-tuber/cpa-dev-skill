You are {{PROJECT_ROLE}}.

---

# Project Information
- Project: {{PROJECT_NAME}}
- Homepage: {{HOMEPAGE_URL}}
- GitHub: {{REPOSITORY_URL}}
- Language: {{TARGET_LANGUAGE}}

---

# Project Profile

- Scale    : {{PROJECT_SCALE}}    # small / medium / large
- Category : {{PROJECT_CATEGORY}} # system / software / other

See docs/cpa-project-profile.md for definitions and selection guide.

---

# Project Folder
- {{WORKSPACE_PATH}}
  - backlog : Backlog management folder
  - mcp     : MCP server management folder (if applicable)
  - work    : Application / middleware / library development folder

---

# System Architecture

Follow the CPA (Core Projection Architecture) fractal project structure:
- https://github.com/lambda-tuber/core-projection-architecture/tree/main/cpa_thesis_appendix_B/05_cpa-fractal

Follow the software architecture defined by Core Projection Architecture (CPA):
- https://github.com/lambda-tuber/core-projection-architecture
- https://github.com/lambda-tuber/core-projection-architecture/blob/main/cpa_thesis_appendix_b.md

For shared CPA vocabulary and layer responsibility definitions, refer to:
- docs/cpa-core-vocabulary.md (in this repository)

---

# Development Process

- Iterative development is used.
- Requirements are managed in `backlog/BACKLOG.md`. Management IDs use the format PB-NNNN (e.g. PB-0001, PB-0002).
- PB implementation status is managed in `backlog/STATUS.md`.
- Per-PB information is managed in `backlog/PB-XXXX/` folders (e.g. `backlog/PB-0001/`).
- CR lists and status per PB are managed in `backlog/PB-XXXX/PB-STATUS.md`. Management IDs use the format CR-NNNN (e.g. CR-0001, CR-0002).
- Each CR has its own Markdown file for requirements, progress, and status (e.g. `backlog/PB-0001/CR-0001.md`).

## Iteration / Phase Transition Rules

When transitioning between iterations or phases, the following 2 steps **must** be performed.
Do not proceed autonomously to the next step without explicit instruction from the user.

1. **Record the current state in the STATUS file**
   - Overall PB iteration status → `backlog/STATUS.md`
   - Per-CR status → `backlog/PB-XXXX/PB-STATUS.md`
   - Detailed CR status → `backlog/PB-XXXX/CR-XXXX.md`

2. **Load the detail file for the next iteration / phase**
   - Read the corresponding `prompts/*.md` file before starting work.
   - Refer to the phase definitions in the "Iterations and Phases" section below.

### Prompt File List
- PB Iteration      : see `prompts/pb_iteration.md`
  - PB Open phase   : see `prompts/pb_open.md`
  - CR Open phase   : see `prompts/cr_open.md`
  - CR Iteration    : see `prompts/cr_iteration.md`
    - Requirements  : see `prompts/cr_requirements.md`
    - Design        : see `prompts/cr_design.md`
    - Dev Loop      : see `prompts/cr_dev_loop.md`
      - Coding      : see `prompts/cr_coding.md`
      - Build       : see `prompts/cr_build.md`
      - Test        : see `prompts/cr_test.md`
    - CR Close      : see `prompts/cr_close.md`
  - PB Close phase  : see `prompts/pb_close.md`

---

# CPA Convergence

This section defines how CPA layer boundaries are applied in the specific language
and toolchain used by this project. Fill in the details from the appropriate adapter
in `skills/cpa-dev-skill/adapters/{{TARGET_LANGUAGE}}/`, then keep the merged
guidance here in this root `AGENTS.md`.

## Layer Mapping for {{TARGET_LANGUAGE}}

<!-- Replace with content from skills/cpa-dev-skill/adapters/{{TARGET_LANGUAGE}}/AGENTS.md -->

| CPA Layer | Module / Package | Notes |
|-----------|-----------------|-------|
| CoreModel | | |
| ProjectedContext | | |
| ApplicationBase | | |
| Interface/Boot | | |

## Build and Test Commands

- Build: `{{BUILD_COMMAND}}`
- Test:  `{{TEST_COMMAND}}`

## Tool / Agent Assumptions

{{TOOL_INVOCATION_POLICY}}

{{CODEX_TOOL_NOTES}}

<!-- Describe MCP tool usage, file system conventions, sandbox / approval rules,
     and agent assumptions specific to this project's environment. -->

---
