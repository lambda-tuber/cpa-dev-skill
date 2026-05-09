You are the developer of sample-haskell-cpa-project.

---

# Project Information

- Project: sample-haskell-cpa-project
- Homepage: https://example.com/sample-haskell-cpa-project
- Repository: https://example.com/sample-haskell-cpa-project.git
- Language: haskell

---

# Project Profile

- Scale    : small    # small / medium / large
- Category : software # system / software / other

See `docs/cpa-project-profile.md` for definitions and the selection guide.

---

# Workspace Layout

- examples/sample-haskell-cpa-project
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
- Language adapter: `adapters/haskell/`

The concrete mapping was merged from
`skills/cpa-dev-skill/adapters/haskell/AGENTS.md` for a small scale project.

| CPA Layer | Module / Package | Notes |
|-----------|------------------|-------|
| CoreModel | Sample.CPA.CoreModel.Type | Pure types, IOFunc DI record, AppContext alias |
| CoreModel | Sample.CPA.CoreModel.Constant | Pure constants if needed |
| ProjectedContext | Sample.CPA.ProjectedContext.ExampleUseCase | Domain logic, pure or IO via IOFunc |
| ApplicationBase | Sample.CPA.ApplicationBase.Control | Monad-stack unwrap and use-case runners |
| Interface | Sample.CPA.Interface.FileIO | Concrete IO implementation of IOFunc |
| Interface | Sample.CPA.Interface.StdIO | Optional CLI/stdout implementation |
| Boot | Sample.CPA | Public API facade; assembles IOFunc and calls Control |

Scale: small
Reason: Small sample project used to validate cpa-dev-skill initialization.

---

# Build and Test Commands

- Build: `cabal build`
- Test:  `cabal test`

---

# Tool Invocation Policy

Use the local shell available in the agent environment. Ask for human approval before phase transitions and before commands that mutate external state.

For Claude Code, treat this CLAUDE.md as project memory and follow the prompt entrypoints listed above.


