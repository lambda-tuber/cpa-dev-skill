# CPA Convergence — Python

This file provides the content for the **CPA Convergence** section of your
project's `AGENTS.md`. Copy the relevant sections into your project's `AGENTS.md`
and fill in the placeholders.

Only Python-specific details belong here. For CPA core vocabulary and
layer responsibility definitions, see `docs/cpa-core-vocabulary.md`.
For scale definitions and the selection guide, see `docs/cpa-project-profile.md`.

---

## How to Use This File

1. Open your project's `AGENTS.md`.
2. Locate (or create) the `## CPA Convergence` section.
3. Copy the template block below that matches your project's declared scale.
4. Replace all `{{PLACEHOLDER}}` values with project-specific information.
5. Delete the scale blocks you are not using.

---

## Template: small scale

```markdown
## CPA Convergence

### CPA Layer Mapping (Python — small)

Scale: small
See docs/cpa-project-profile.md for the scale definition and selection guide.

| CPA Layer        | Module                                                    | Class                     | Notes |
|------------------|-----------------------------------------------------------|---------------------------|-------|
| CoreModel        | `{{PKG}}/core_model/type.py`                             | `@dataclass` types        | No IO. Properties permitted. |
| CoreModel        | `{{PKG}}/core_model/constant.py`                         | (none)                    | `_ALL_CAPS` constants only |
| CoreModel        | `{{PKG}}/core_model/logging_config.py`                   | (functions)               | IO exception: cross-cutting observability |
| CoreModel/port   | `{{PKG}}/core_model/port/{{role}}.py`                    | `{{Role}}(Protocol)`      | Capability abstraction. No IO. |
| ProjectedContext | `{{PKG}}/projected_context/{{use_case}}_service.py`      | `{{UseCase}}Service`      | Structurally satisfies CoreModel/port Protocol |
| ApplicationBase  | `{{PKG}}/application_base/control.py`                    | `ApplicationBase`         | src→work→sink. Constructor DI. |
| Interface        | `{{PKG}}/interface/{{backend}}.py`                       | `{{Backend}}`             | Concrete. No inheritance from Protocol. |
| Boot             | `{{PKG}}/boot/bootstrap.py`                              | `Bootstrap`               | DI container. Knows all concrete classes. |
| Boot (entry)     | `{{PKG}}/__main__.py`                                    | `Main`                    | argparse → ArgData → Bootstrap().run() |

Package name: `{{PKG}}`
  Example: `sample_project`

### Build / Test Commands

- Build: `python -m py_compile $(find src -name "*.py")`
- Test:  `python -m pytest`
- Run:   `python -m {{PKG}} -y {{CONFIG_YAML}} --agent {{AGENT_NAME}}`

### Scale

- Scale: small
- Reason: {{REASON}}
  Example: Single package AI agent with YAML config, constructor DI, and
           one-shot request/response loop in ApplicationBase.

### DI Flow

```
Bootstrap.run()
  ├─ _load_config()         → AgentRegistry (CoreModel)
  ├─ _build_backends()      → dict[str, {{BackendProtocol}}] (CoreModel/port)
  ├─ {{UseCase}}Service()   → dict[str, {{UseCase}}Service] (ProjectedContext)
  ├─ registry.agents ← DI inject                     ← DI point ①
  ├─ _create_cli_adapter()  → {{CliAdapter}} (Interface)
  └─ ApplicationBase(registry, cli_adapter)          ← DI point ②
       └─ app.run()
            └─ registry.active_{{entity}}.{{action}}(input)
```

### Port Placement Rule

Port (Protocol) definitions are placed in `core_model/port/`, NOT in
`projected_context/port/`. The capability abstraction of a domain entity is a
domain concept (Ontological CoreModel principle).
See `adapters/python/README.md §4` for the full rationale.

### Protocol vs ABC Rule

Cross-layer boundaries use `typing.Protocol` + `@runtime_checkable`.
Interface concrete classes do **not** inherit from the Protocol — they satisfy
it structurally. This prevents reverse dependency arrows.

### Dependency Direction

```
Boot → ApplicationBase → CoreModel
Boot → ProjectedContext ({{UseCase}}Service concrete only)
Boot → Interface (concrete classes only)
ProjectedContext → CoreModel (implements port/ Protocol)
Interface → CoreModel (structurally satisfies port/ Protocol)
```

Forbidden:
- `core_model.*` importing any other layer
- `projected_context.*` importing `interface.*`
- `interface.*` importing `projected_context.*`
- `application_base.*` importing `interface.*` or `projected_context.*`
- Any layer other than `boot.*` importing Interface concrete classes

### Erosion Analysis

- Configuration: {{Semantic | Ontological}}
- CoreModel types are valid without IO substrate: {{Yes | No}}
- IO boundary: `ApplicationBase` (via constructor-injected Port Protocols)

### Naming Conventions

- Package: `{{snake_case_pkg}}`
- CPA layer folders: `core_model/`, `projected_context/`, `application_base/`, `interface/`, `boot/`
- Port folder: `core_model/port/`
- Protocol class names: `<Role>` — no Port/I/Base suffix (e.g. `Agent`, `AiBackend`)
- UseCase class names: `<Domain>Service` (e.g. `AgentService`)
- dataclass names: `PascalCase` (e.g. `AgentRegistry`, `ArgData`)
- Constants: `_ALL_CAPS` (e.g. `_BACKEND_OPENAI`, `_DEFAULT_DEBUG_LEVEL`)
- Logger: `get_logger(__name__)` in every module
```

---

## Template: medium scale

```markdown
## CPA Convergence

### CPA Layer Mapping (Python — medium)

Scale: medium
See docs/cpa-project-profile.md for the scale definition and selection guide.

| CPA Layer        | Module                                                    | Class                     | Notes |
|------------------|-----------------------------------------------------------|---------------------------|-------|
| CoreModel        | `{{PKG}}/core_model/type.py`                             | `@dataclass` types        | State machine types, event types if needed |
| CoreModel        | `{{PKG}}/core_model/constant.py`                         | (none)                    | `_ALL_CAPS` constants |
| CoreModel        | `{{PKG}}/core_model/logging_config.py`                   | (functions)               | IO exception: observability |
| CoreModel/port   | `{{PKG}}/core_model/port/{{role}}.py`                    | `{{Role}}(Protocol)`      | Capability abstraction |
| ProjectedContext | `{{PKG}}/projected_context/{{use_case}}_service.py`      | `{{UseCase}}Service`      | Domain logic; may include async |
| ApplicationBase  | `{{PKG}}/application_base/control.py`                    | `ApplicationBase`         | Async loop / state machine; constructor DI |
| Interface        | `{{PKG}}/interface/{{backend}}.py`                       | `{{Backend}}`             | Concrete; no Protocol inheritance |
| Boot             | `{{PKG}}/boot/bootstrap.py`                              | `Bootstrap`               | DI container; thread/async startup |
| Boot (entry)     | `{{PKG}}/__main__.py`                                    | `Main`                    | argparse → ArgData → Bootstrap().run() |

Package name: `{{PKG}}`

### Build / Test Commands

- Build: `python -m py_compile $(find src -name "*.py")`
- Test:  `python -m pytest`
- Run:   `python -m {{PKG}} -y {{CONFIG_YAML}}`

### Scale

- Scale: medium
- Reason: {{REASON}}
  Example: Single package with async event loop in ApplicationBase,
           multiple agent states, or structured pipeline.

### DI Flow

```
Bootstrap.run()
  ├─ _load_config()          → {{RegistryType}} (CoreModel)
  ├─ _build_backends()       → dict[str, {{BackendProtocol}}]
  ├─ {{UseCase}}Service()    → dict[str, {{UseCase}}Service]
  ├─ registry.{{agents}} ← DI inject
  ├─ _create_{{adapter}}()   → {{AdapterProtocol}} concrete
  └─ ApplicationBase(registry, adapter).run()   ← async or sync
```

### Port Placement Rule

Same as small scale: Ports in `core_model/port/`.

### Protocol vs ABC Rule

Same as small scale: `typing.Protocol` + `@runtime_checkable` for all
cross-layer boundaries.

### Dependency Direction

Same rules as small scale. If async is introduced, `asyncio` types must not
leak into CoreModel.

### Erosion Analysis

- Configuration: {{Semantic | Ontological}}
- Reason: {{REASON}}

### Naming Conventions

Same as small scale.
```

---

## Template: large scale

```markdown
## CPA Convergence

### CPA Layer Mapping (Python — large)

Scale: large
See docs/cpa-project-profile.md for the scale definition and selection guide.

| CPA Layer        | Package / Module                         | Class              | Notes |
|------------------|------------------------------------------|--------------------|-------|
| CoreModel        | `{{core_pkg}}/core_model/`              | `@dataclass`, `Protocol` | Shared across sub-packages |
| ProjectedContext | `{{projected_pkg}}/projected_context/`  | `{{UseCase}}Service` | Depends on CoreModel only |
| ApplicationBase  | `{{app_pkg}}/application_base/`         | `ApplicationBase`  | Orchestration |
| Interface        | `{{iface_pkg}}/interface/`              | `{{Backend}}`      | Concrete adapters |
| Boot             | `{{boot_pkg}}/boot/`                    | `Bootstrap`        | DI; knows all packages |

Note: Each sub-package internally follows the same five-layer structure
(Fractal Recursion). Retro-dependency is enforced by import linting (e.g.
`import-linter` or `flake8-import-order`) rather than the build system.

### Build / Test Commands

- Build: `python -m py_compile $(find src -name "*.py")`
- Test:  `python -m pytest`
- Run:   `python -m {{boot_pkg}}`

### Scale

- Scale: large
- Reason: {{REASON}}
  Example: Multi-component system with separate deployment units.
           Retro-dependency enforced by CI import-linter rules.

### Port Placement Rule

Each sub-package defines its own `core_model/port/` for that sub-package's
domain capabilities. Shared cross-package Protocols go in the shared CoreModel
package.

### Dependency Direction

Same rules as small scale, applied per sub-package. Boot is always the outermost
layer and the only one permitted to assemble the full object graph.

### Erosion Analysis

- Configuration: {{Semantic | Ontological | Mixed}}
- Reason: {{REASON}}
  Record per sub-package if configurations differ.

### Naming Conventions

Same as small scale within each sub-package.
Sub-package naming: `<domain>_<layer_role>` (e.g. `agent_core`, `agent_interface`).
```

---

## Reference

- Scale definitions and selection guide: `docs/cpa-project-profile.md`
- CPA layer vocabulary: `docs/cpa-core-vocabulary.md`
- Python implementation guide: `adapters/python/README.md`
- Haskell implementation guide: `adapters/haskell/README.md`
- AGENTS.md template: `templates/AGENTS.md`
- Reference implementation: `adapters/python/README.md`
