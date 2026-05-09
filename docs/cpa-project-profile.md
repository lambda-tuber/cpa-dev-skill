# CPA Project Profile: Scale and Category Definitions

This document defines the **project profile** — the foundational assumptions that
govern how Core Projection Architecture (CPA) is applied throughout a project.

The profile must be declared in `AGENTS.md` at project initialization and
confirmed at every PB open. If a CR reveals a mismatch, escalate to the PB level
before proceeding.

---

## Scale

Scale describes the structural complexity of the project and determines which
CPA construction pattern to use.

| Scale | Value | Structure | Typical Use Case |
|-------|-------|-----------|-----------------|
| Small | `small` | Single library — four CPA layers expressed as modules within one package | Public API library, utility library |
| Medium | `medium` | Single package — four layers plus non-functional concerns (logging, state machine, async pipeline) | Standalone application or service |
| Large | `large` | Multi-package fractal structure — package boundaries enforce layer boundaries | Multi-component system requiring strict Retro-dependency at the package level |

### Scale Selection Guide

Answer the following questions in order. Stop at the first **Yes**.

```
1. Does the project require multiple independently deployable or composable
   packages where Retro-dependency must be enforced at the build-system level?
   → Yes: large

2. Does the ApplicationBase layer require any of the following non-functional
   concerns?
     - Stateful processing (state machine, GADT-based transitions)
     - Structured logging (e.g. LoggingT, structured log monad)
     - Streaming / async pipeline (e.g. Conduit, async, event loop)
   → Yes: medium

3. None of the above apply.
   → small
```

### Scale and CPA Construction

| Scale | CPA Construction |
|-------|-----------------|
| `small` | Four-layer module split within a single library. ApplicationBase contains only a use-case runner — no state machine, no logging, no pipeline. |
| `medium` | Four layers plus non-functional infrastructure within a single package. ApplicationBase wires Conduit pipeline, state machine (GADT/TH), and logging (LoggingT). |
| `large` | Fractal seven-package structure. Each package represents one layer or one component. `build-depends` enforces Retro-dependency statically. |

> For language-specific skeleton starting points, see `adapters/<language>/README.md`.

---

## Category

Category describes the nature of the project's primary deliverables and
determines how CPA layers are interpreted and validated.

| Category | Value | Description | CPA Application Notes |
|----------|-------|-------------|----------------------|
| System | `system` | Infrastructure, IaC, environment configuration, CI/CD pipelines | The execution environment itself shapes CoreModel (Ontological Configuration is common). Layer boundaries map to environment boundaries rather than code modules. |
| Software | `software` | Libraries, applications, services — implementation in a programming language | All four CPA layers exist as implementation code. Retro-dependency can be enforced by the compiler and package system. |
| Other | `other` | Documents, specifications, guides, skills, templates, or any composite work not covered above | Executable code may not exist. CoreModel organises domain concepts rather than types. Erosion analysis targets concept leakage rather than import direction. |

### Category Selection Guide

```
1. Is the primary deliverable the configuration or orchestration of
   an execution environment (servers, pipelines, infrastructure)?
   → Yes: system

2. Is the primary deliverable implemented source code (library, application,
   service) that runs in a language runtime?
   → Yes: software

3. Otherwise (documents, skill files, templates, mixed, or other):
   → other
```

---

## Declaring the Project Profile

In `AGENTS.md`, declare the profile in the **Project Profile** section:

```markdown
## Project Profile

- Scale    : small       # small / medium / large
- Category : software    # system / software / other

See docs/cpa-project-profile.md for definitions and selection guide.
```

### Rules

- Declare at **project initialization** (skill_init, Step 1).
- Confirm at **every PB open** (pb_open, Project Profile Check).
- If a CR requirements phase reveals a mismatch with the declared profile,
  **do not adjust silently** — escalate to the PB level and update `AGENTS.md`
  before continuing.

---

## References

- [CPA Core Vocabulary](cpa-core-vocabulary.md)
- [AGENTS.md template](../templates/AGENTS.md)
- [skill_init prompt](../prompts/skill_init.md)
- [pb_open prompt](../prompts/pb_open.md)
