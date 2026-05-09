# cpa-dev-skill

A language-neutral skill repository for starting and sustaining CPA-compliant
software development with human-AI collaboration.

---

## What is cpa-dev-skill?

`cpa-dev-skill` provides the initialization templates, prompt sets, and adapter
documents needed to apply **Core Projection Architecture (CPA)** to any software
project — regardless of language or toolchain.

CPA is not a derivative of Clean Architecture or Onion Architecture.
It is an independent theory grounded in **manifestation**, **discovery**, and
**projection**: software structure is _discovered_ from the domain, not invented;
use cases are _projections_ of the Core model, not independent logic; and
dependencies must flow strictly in the direction opposite to manifestation.

This repository gives you the scaffolding to adopt that theory in practice,
collaboratively with an AI agent.

---

## Why CPA helps human-AI collaboration

When humans and AI agents work together on a codebase, misunderstandings about
_where_ a concept belongs cause the most friction. CPA addresses this directly:

- **Shared vocabulary** — Terms like _Core_, _Projection_, _Retro-dependency_,
  and _Erosion_ are defined precisely in `docs/cpa-core-vocabulary.md`.
  Both humans and AI agents use the same words with the same meaning.

- **Structural prompts** — Instead of vague instructions ("make this cleaner"),
  you can give precise instructions: "this belongs in CoreModel, not ProjectedContext"
  or "you have introduced an Erosion — the type stack has leaked into Core."

- **Repeatable process** — The PB / CR iteration workflow in `prompts/` gives
  both humans and AI agents a shared process that scales from a single library
  to a fractal multi-package system.

---

## Quick Start

1. **Clone this repository** (or copy it into your workspace).

2. **Copy `templates/AGENTS.md`** into your target project as `AGENTS.md`
   and fill in all `{{PLACEHOLDER}}` values. If the project also uses Claude
   Code or GitHub Copilot, copy `templates/CLAUDE.md` or
   `templates/.github/copilot-instructions.md` to the corresponding target path.

3. **Copy `docs/` and `prompts/`** into your target project.
   The instruction files use repository-relative paths such as `docs/...`,
   and `prompts/...`, so these directories must be
   available from the target project root.

4. **Copy `templates/backlog/BACKLOG.md` and `templates/backlog/STATUS.md`**
   into your target project to initialize the backlog. PB folders and CR files
   are created later by the PB Open and CR Open phases.

5. **Pick an adapter** from
   `skills/cpa-dev-skill/adapters/<language>/` that matches your project's
   language and merge its CPA Convergence section into your `AGENTS.md`.
   Do not copy the `adapters/` directory into the target project by default.

6. **Open an AI agent session** with `AGENTS.md` loaded, then instruct the agent:
   ```
   Read prompts/pb_iteration.md and prepare to open the first PB.
   ```

For the full step-by-step guide, see [SKILL.md](SKILL.md).

---

## Minimum CPA Vocabulary

Before starting, align on these four terms.
Full definitions are in [`docs/cpa-core-vocabulary.md`](docs/cpa-core-vocabulary.md).

| Term | One-line definition |
|------|---------------------|
| **Core** | The discovered domain model: pure types, invariants, and value objects. No IO, no frameworks. |
| **Projection** | A use-case-level view _projected_ from Core. Not an invention — a selection from what Core already defines. |
| **Retro-dependency** | Dependencies flow outward-to-inward only: Interface → ApplicationBase → ProjectedContext → Core. Never the reverse. |
| **Erosion** | Unintended leakage of outer-layer concerns into inner layers. Two kinds: Semantic (type stack pollution) and Ontological (domain concept misplacement). |

---

## Repository Layout

```
cpa-dev-skill/
├── README.md                  ← You are here
├── SKILL.md                   ← Initialization skill definition
├── docs/
│   └── cpa-core-vocabulary.md ← CPA vocabulary and layer responsibility guide
├── templates/
│   ├── AGENTS.md              ← AI agent instruction template (copy to project root)
│   ├── CLAUDE.md              ← Claude Code project memory template
│   ├── .github/
│   │   └── copilot-instructions.md ← GitHub Copilot repository instructions
│   └── backlog/
│       ├── BACKLOG.md         ← Product backlog template
│       ├── STATUS.md          ← Project status template
│       ├── PB-STATUS.md       ← Per-PB CR list template
│       └── CR-XXXX.md         ← CR detail template (CR ID: CR-0001, CR-0002, ...)
├── prompts/                   ← Language-neutral phase prompt set (CR-09)
├── adapters/                  ← Language-specific CPA enforcement guides (CR-10+)
└── examples/                  ← Sample initialization flows (CR-12)
```

---

## Initialization Flow

```
┌────────────────────────────────────────────────────────┐
│  1. Fill templates/AGENTS.md  →  project/AGENTS.md     │
│  2. Copy docs/, prompts/           → project/          │
│  3. Copy BACKLOG.md, STATUS.md     → project/backlog/  │
│  4. Read docs/cpa-core-vocabulary.md (shared vocab)    │
│  5. Merge adapter guidance into AGENTS.md              │
│  6. Prepare the first PB iteration with the AI agent   │
└────────────────────────────────────────────────────────┘
```

See [SKILL.md](SKILL.md) for the detailed step-by-step procedure.

---

## Adapters

`adapters/` is a first-class directory in this repository.

Each adapter document explains:
- How CPA layers map to modules / packages in a specific language
- How to enforce Retro-dependency (compiler, linter, or convention)
- How to prevent Erosion in that language's idiom
- Language-specific build/test commands and tool assumptions

### Current Adapters

| Language | Status | Location |
|----------|--------|----------|
| Haskell | Available | `adapters/haskell/` |

---

## References

- [Core Projection Architecture](https://github.com/lambda-tuber/core-projection-architecture)
- [CPA Appendix B](https://github.com/lambda-tuber/core-projection-architecture/blob/main/cpa_thesis_appendix_b_en.md)
- [CPA Core Vocabulary](docs/cpa-core-vocabulary.md)
- [SKILL.md](SKILL.md)
- [AGENTS.md template](templates/AGENTS.md)

---

## Credits & License

- **Execution & Process Lead:** Sonnet 4.6, Gemini 3 Flash, GPT-5.5
- **Direction & Policy:** lambda-tuber
- **License:** MIT -- see [LICENSE](./LICENSE)
