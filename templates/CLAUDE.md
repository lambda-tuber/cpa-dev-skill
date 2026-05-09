You are {{PROJECT_ROLE}}.

---

# Project Information

- Project: {{PROJECT_NAME}}
- Homepage: {{HOMEPAGE_URL}}
- Repository: {{REPOSITORY_URL}}
- Language: {{TARGET_LANGUAGE}}

---

# Project Profile

- Scale    : {{PROJECT_SCALE}}    # small / medium / large
- Category : {{PROJECT_CATEGORY}} # system / software / other

See `docs/cpa-project-profile.md` for definitions and the selection guide.

---

# Workspace Layout

- {{WORKSPACE_PATH}}
  - backlog : Backlog management folder
  - mcp     : MCP server management folder (if applicable)
  - work    : Application / middleware / library development folder

---

# CPA Workflow

Use iterative PB / CR development.

- Product Backlog items are managed in `backlog/BACKLOG.md`.
- PB status is managed in `backlog/STATUS.md`.
- Per-PB CR status is managed in `backlog/PB-XXXX/PB-STATUS.md`.
- Each CR has a detail file such as `backlog/PB-0001/CR-0001.md`.

Before moving between phases:

1. Record the current state in the appropriate status files.
2. Read the prompt file for the next phase.
3. Wait for explicit human approval before proceeding to the next phase.

---

# Prompt Entrypoints

- PB Iteration      : `prompts/pb_iteration.md`
  - PB Open phase   : `prompts/pb_open.md`
  - CR Open phase   : `prompts/cr_open.md`
  - CR Iteration    : `prompts/cr_iteration.md`
    - Requirements  : `prompts/cr_requirements.md`
    - Design        : `prompts/cr_design.md`
    - Dev Loop      : `prompts/cr_dev_loop.md`
      - Coding      : `prompts/cr_coding.md`
      - Build       : `prompts/cr_build.md`
      - Test        : `prompts/cr_test.md`
    - CR Close      : `prompts/cr_close.md`
  - PB Close phase  : `prompts/pb_close.md`

---

# CPA Convergence

Apply CPA layer boundaries according to the selected adapter:

- Shared vocabulary: `docs/cpa-core-vocabulary.md`
- Project profile: `docs/cpa-project-profile.md`
- Language adapter: `adapters/{{TARGET_LANGUAGE}}/`

Fill the concrete layer mapping from `adapters/{{TARGET_LANGUAGE}}/AGENTS.md`.

| CPA Layer | Module / Package | Notes |
|-----------|-----------------|-------|
| CoreModel | | |
| ProjectedContext | | |
| ApplicationBase | | |
| Interface/Boot | | |

---

# Build and Test Commands

- Build: `{{BUILD_COMMAND}}`
- Test:  `{{TEST_COMMAND}}`

---

# Tool Invocation Policy

{{TOOL_INVOCATION_POLICY}}

{{CLAUDE_TOOL_NOTES}}

