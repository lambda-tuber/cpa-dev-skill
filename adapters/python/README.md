# CPA Python Adapter

This document describes how Core Projection Architecture (CPA) layer boundaries
are enforced in a **Python** project. All content here is Python-specific.
Language-neutral CPA concepts belong to `docs/cpa-core-vocabulary.md`.

Reference implementation: `sample-project` (fully tested, pytest all-pass).

---

## 1. Scale Reference

Project scale determines which CPA construction pattern to use.

> **Scale definitions and the selection guide are maintained in
> `docs/cpa-project-profile.md`.** Do not duplicate them here.

This adapter describes how each scale maps to **Python-specific** package and
class structures.

| Scale | Python structure |
|-------|-----------------|
| `small` | Single package. Five CPA layers expressed as **sub-packages** under `src/<pkg>/`. OOP class-per-layer pattern. |
| `medium` | Single package with richer ApplicationBase (async loop, state machine). Same sub-package layout. |
| `large` | Multi-package layout. Import discipline enforced by linting / CI rather than the build system. |

---

## 2. Package Structure (Folder / File Layout)

### small — single package, class-per-layer

```
src/
  <package_name>/
    __init__.py
    __main__.py                 # Main class + module-level main() wrapper
    core_model/
      __init__.py
      type.py                   # @dataclass only. No IO, no logging.
      constant.py               # _ALL_CAPS constants
      logging_config.py         # get_logger() / setup_logging()  [IO exception — see §9]
      utility.py                # pure helper functions (optional)
      port/
        __init__.py
        <role>.py               # Protocol definition (capability abstraction)
    projected_context/
      __init__.py
      <use_case>_service.py     # UseCase class — structurally satisfies CoreModel/port Protocol
    application_base/
      __init__.py
      control.py                # ApplicationBase class (constructor DI)
    interface/
      __init__.py
      <backend>.py              # Concrete class — structurally satisfies CoreModel/port Protocol
    boot/
      __init__.py
      bootstrap.py              # Bootstrap class + module-level run() wrapper

tests/
  <package_name>/
    __init__.py
    core_model/
      test_type.py
      test_constant.py
      test_logging_config.py
    projected_context/
      test_<use_case>_service.py
    application_base/
      test_control.py
    interface/
      test_<backend>.py
    boot/
      test_bootstrap.py
      test_main.py

pyproject.toml                  # dependencies: PyYAML / pytest
```

### `sample-project` confirmed file list

| File | CPA Layer | Role |
|------|-----------|------|
| `core_model/type.py` | CoreModel | `ItemRegistry`, `ItemConfig`, `ArgData`, `SystemConfig` dataclasses |
| `core_model/constant.py` | CoreModel | `_BACKEND_DEFAULT` etc. |
| `core_model/logging_config.py` | CoreModel | `get_logger()` / `setup_logging()` |
| `core_model/port/item.py` | CoreModel/port | `Item(Protocol)` |
| `core_model/port/backend.py` | CoreModel/port | `Backend(Protocol)` |
| `core_model/port/cli_adapter.py` | CoreModel/port | `CliAdapter(Protocol)` |
| `projected_context/item_service.py` | ProjectedContext | `ItemService` (structurally satisfies `Item`) |
| `application_base/control.py` | ApplicationBase | `ApplicationBase` class |
| `interface/default_backend.py` | Interface | `DefaultBackend` (structurally satisfies `Backend`) |
| `interface/alt_backend.py` | Interface | `AltBackend` (structurally satisfies `Backend`) |
| `interface/cli_adapter.py` | Interface | `CliAdapter` concrete (structurally satisfies `CliAdapter` Protocol) |
| `boot/bootstrap.py` | Boot | `Bootstrap` class |
| `__main__.py` | Boot (entry) | `Main` class |

---

## 3. Layer-to-Module / Class Mapping

| CPA Layer | Python Module | Python Class | Responsibility |
|-----------|--------------|-------------|----------------|
| CoreModel | `core_model/type.py` | `@dataclass` types only | No IO, no logic beyond properties |
| CoreModel | `core_model/constant.py` | (no class needed) | `_ALL_CAPS` constants only |
| CoreModel | `core_model/logging_config.py` | (functions) | cross-cutting IO exception (§9) |
| CoreModel/port | `core_model/port/<role>.py` | `<Role>(Protocol)` | Capability abstraction only. No IO. |
| ProjectedContext | `projected_context/<use_case>_service.py` | `<UseCase>Service` | Structurally implements CoreModel/port Protocol. UseCase orchestration. |
| ApplicationBase | `application_base/control.py` | `ApplicationBase` | src→work→sink pass. Constructor DI receives registry + cli_adapter. |
| Interface | `interface/<backend>.py` | `<Backend>` | Concrete class. Structurally satisfies CoreModel/port Protocol. No inheritance. |
| Boot | `boot/bootstrap.py` | `Bootstrap` | DI container. The only layer that knows all concrete classes. |
| Boot (entry) | `__main__.py` | `Main` | argparse → ArgData → Bootstrap().run() |

---

## 4. Port Placement — Ontological CoreModel Principle (Critical)

**Port (Protocol) definitions go in `core_model/port/`. NOT in `projected_context/port/`.**

### Background

In Haskell CPA the `World m` type class (≡ Port) is defined at the CoreModel
level. "The capability abstraction of an AI agent is a domain concept and
belongs in CoreModel" — this is the CPA Ontological principle.

Placing Port in `projected_context/port/` (a common mistake when coming from
general Hexagonal Architecture) causes a structural mismatch: Interface concrete
classes would need to import from ProjectedContext, creating a retro-dependency.

### Confirmed layout (`sample-project`)

```
core_model/port/
  item.py          Item(Protocol)        ← ≡ Haskell World m type class
  backend.py       Backend(Protocol)
  cli_adapter.py   CliAdapter(Protocol)
```

`core_model/port/` contains Protocol definitions only — no IO, no framework
dependency. CoreModel purity is fully preserved.

### Reference implementation

```python
# core_model/port/item.py
from typing import Protocol, runtime_checkable

@runtime_checkable
class Item(Protocol):
    """Ontological abstraction of a domain entity.
    Equivalent to the Haskell World m type class.
    Any class with a matching process() signature satisfies this structurally.
    """
    def process(self, user_input: str) -> str: ...
```

```python
# core_model/port/ai_backend.py
from typing import Protocol, runtime_checkable

@runtime_checkable
class AiBackend(Protocol):
    def connect(self) -> None: ...
    def disconnect(self) -> None: ...
    def send(self, prompt: str) -> str: ...
```

```python
# core_model/port/cli_adapter.py
from typing import Protocol, runtime_checkable

@runtime_checkable
class CliAdapter(Protocol):
    def prompt_input(self, message: str) -> str: ...
    def display_output(self, message: str) -> None: ...
    def display_error(self, message: str) -> None: ...
```

### Port naming rule

The `port/` package name itself signals "interface definitions here."
Do **not** add `Port` suffix, `I` prefix, or `Base` suffix to class names.
`protocol/` is also rejected — it names the Python typing feature, not the
architectural role.

| Wrong | Correct | Reason |
|-------|---------|--------|
| `AiBackendPort` | `AiBackend` | suffix is redundant; package name carries the meaning |
| `IAgent` | `Agent` | Hungarian prefix not used in Python |
| `protocol/` (folder) | `port/` (folder) | folder names the role, not the implementation mechanism |

---

## 5. Protocol vs ABC — Enforcing Dependency Direction

**Use `typing.Protocol` (structural subtyping) for cross-layer boundaries.**

Python's `Protocol` has semantics close to Haskell type classes. Concrete
classes do not need to import or inherit from the Protocol definition, so the
dependency arrow never points inward.

```python
# WRONG: ABC inheritance forces Interface to import from core_model.port
class OpenAiBackend(AiBackend):   # import of core_model.port is required
    ...

# CORRECT: Protocol — no inheritance, Interface imports nothing from core_model.port
class OpenAiBackend:              # zero import from core_model.port
    def connect(self) -> None: ...
    def disconnect(self) -> None: ...
    def send(self, prompt: str) -> str: ...
    # Structurally satisfies AiBackend Protocol without inheriting
```

### `@runtime_checkable` usage

Adding `@runtime_checkable` to a Protocol allows Boot to verify DI-injected
objects at runtime via `isinstance(obj, Agent)` without requiring inheritance.
The Interface concrete class itself still imports nothing from `core_model.port`.

### Rule summary

| Location | Type to use | Reason |
|----------|-------------|--------|
| Cross-layer port definitions (`core_model/port/*`) | `typing.Protocol` + `@runtime_checkable` | No forced inheritance import. Dependency direction guaranteed by structure. |
| Intra-package strong abstraction (optional) | `ABC` | Only when explicit abstract method enforcement is needed within the same package. |

### Confirmed in `sample-project`

| Protocol | Concrete class | Inherits? |
|---------|---------------|-----------|
| `core_model/port/backend.py :: Backend` | `interface/default_backend.py :: DefaultBackend` | No |
| `core_model/port/backend.py :: Backend` | `interface/alt_backend.py :: AltBackend` | No |
| `core_model/port/cli_adapter.py :: CliAdapter` | `interface/cli_adapter.py :: CliAdapter` | No |
| `core_model/port/item.py :: Item` | `projected_context/item_service.py :: ItemService` | No |

---

## 6. OOP Design Patterns — Class + Constructor DI

Python CPA uses class-based OOP throughout. Module-level functions are retained
only as backward-compatible wrappers.

### ProjectedContext — UseCase Service class

Structurally satisfies a CoreModel/port Protocol. Receives its dependency
(an inner-layer Protocol, e.g. `AiBackend`) via constructor.

```python
# projected_context/item_service.py
from <pkg>.core_model.port.backend import Backend

class ItemService:
    """Structurally satisfies core_model/port/Item (process method matches).
    ProjectedContext UseCase orchestration role.
    """
    def __init__(self, backend: Backend) -> None:
        self._backend = backend

    def process(self, user_input: str) -> str:
        # Pure delegation; cross-backend common logic added here as needed.
        return self._backend.send(user_input)
```

### ApplicationBase — ApplicationBase class (constructor DI)

Receives all dependencies via constructor. Has zero knowledge of concrete
backend or CLI implementations.

```python
# application_base/control.py
from <pkg>.core_model.port.cli_adapter import CliAdapter
from <pkg>.core_model.type import AgentRegistry

class ApplicationBase:
    def __init__(
        self,
        agent_registry: AgentRegistry,
        cli_adapter: CliAdapter,          # CoreModel/port Protocol
    ) -> None:
        self._registry = agent_registry
        self._cli_adapter = cli_adapter

    def run(self) -> None:
        # src: wait for user input
        user_input = self._cli_adapter.prompt_input("> ")
        # work: domain logic (quit-detection etc.) goes here
        # sink: delegate to active agent, display result
        reply = self._registry.active_agent.chat(user_input)
        self._cli_adapter.display_output(reply)
```

### Boot — Bootstrap class (DI container)

```python
# boot/bootstrap.py
class Bootstrap:
    def run(self, args: ArgData) -> None:
        setup_logging(_DEFAULT_DEBUG_LEVEL)
        system_config, agent_registry = self._load_config(args)
        setup_logging(system_config.debug_level)
        backends = self._build_backends(registry.item_configs)
        registry.items = {
            name: ItemService(backend)
            for name, backend in backends.items()
        }
        cli_adapter = self._create_cli_adapter()
        ApplicationBase(registry, cli_adapter).run()

    def _load_config(self, args: ArgData) -> tuple[SystemConfig, ItemRegistry]: ...
    def _build_backends(self, configs: dict[str, ItemConfig]) -> dict[str, Backend]: ...
    def _select_backend(self, config: ItemConfig) -> Backend: ...
    def _create_cli_adapter(self) -> CliAdapter: ...

# Backward-compatible module-level wrapper
def run(args: ArgData) -> None:
    Bootstrap().run(args)
```

### Boot entry — Main class

```python
# __main__.py
class Main:
    def _parse_args(self) -> ArgData: ...
    def main(self) -> None:
        Bootstrap().run(self._parse_args())

def main() -> None:
    Main().main()

if __name__ == "__main__":
    main()
```

---

## 7. DI Flow and Dependency Direction Rules

### DI flow (Bootstrap.run() assembly order)

```
Bootstrap.run()
  ├─ _load_config()             → AgentRegistry (CoreModel)
  ├─ _build_backends()          → dict[str, AiBackend] (CoreModel/port type)
  ├─ ItemService creation        → dict[str, ItemService] (ProjectedContext concrete)
  ├─ registry.items ← DI inject                           ← DI point ①
  ├─ _create_cli_adapter()      → CliAdapter concrete (Interface)
  └─ ApplicationBase(registry, cli_adapter)               ← DI point ②
       └─ app.run()
            └─ registry.active_item.process(user_input)
```

### Dependency direction

```
Boot → ApplicationBase → CoreModel
Boot → ProjectedContext (AgentService concrete only)
Boot → Interface (concrete classes only)
ProjectedContext → CoreModel (implements port/ Protocol)
Interface → CoreModel (structurally satisfies port/ Protocol)
```

### Forbidden patterns (Python-specific)

| Forbidden | Reason |
|-----------|--------|
| `core_model.*` importing any other layer | CoreModel is the innermost layer |
| `projected_context.*` importing `interface.*` | Retro-dependency |
| `interface.*` importing `projected_context.*` | Retro-dependency |
| `application_base.*` importing `interface.*` or `projected_context.*` | ApplicationBase references CoreModel only |
| Any layer other than `boot.*` importing Interface concrete classes | DI is Boot's exclusive responsibility |

### Retro-dependency / Erosion check template

```
- [ ] Boot → ApplicationBase → CoreModel direction maintained
- [ ] core_model/port/ contains Protocol definitions only; no IO/framework dependency
- [ ] ProjectedContext (UseCase concrete) references CoreModel only (correct projection)
- [ ] Interface references CoreModel/port only; does not import ProjectedContext
- [ ] ApplicationBase references CoreModel (AgentRegistry / port/CliAdapter) only
- [ ] Only Boot imports ProjectedContext and Interface concrete classes
```

---

## 8. CoreModel dataclass Design — Properties and Circular Import Guard

### dataclass with property

CoreModel `type.py` consists of `@dataclass` types only. **Properties are
permitted** — they provide post-DI access without compromising CoreModel purity.

```python
# core_model/type.py
from __future__ import annotations
from dataclasses import dataclass, field
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from <pkg>.core_model.port.item import Item

@dataclass
class ItemRegistry:
    item_configs: dict[str, ItemConfig] = field(default_factory=dict)
    active_item_name: str = ""
    items: dict[str, "Item"] = field(default_factory=dict)  # Boot DI-injects

    @property
    def active_item(self) -> "Item":
        return self.items[self.active_item_name]

    @property
    def active_item_config(self) -> ItemConfig:
        return self.item_configs[self.active_item_name]
```

### Circular import guard

`items` field type `Item` is defined in `core_model/port/item.py`. Use
`TYPE_CHECKING` guard + string forward reference in `type.py` to avoid a
circular import at runtime.

```python
from __future__ import annotations
from typing import TYPE_CHECKING
if TYPE_CHECKING:
    from <pkg>.core_model.port.item import Item
```

At runtime the string `"Agent"` is never resolved, so no import occurs.
Type checkers (mypy, pyright) see the full type through `TYPE_CHECKING`.

---

## 9. CoreModel IO Exception Rule — Logging

`core_model/logging_config.py` contains IO (`StreamHandler` addition) but is
permitted in CoreModel as a **cross-cutting observability infrastructure
exception**. All other CoreModel modules must remain IO-free.

Unified pattern across all layers:

```python
from <pkg>.core_model.logging_config import get_logger
logger = get_logger(__name__)
```

---

## 10. Bootstrap Pattern — YAML Loading + DI

Standard flow for `Bootstrap.run()`:

```python
def run(self, args: ArgData) -> None:
    # 1. Provisional logging before YAML is read
    setup_logging(_DEFAULT_DEBUG_LEVEL)

    # 2. Read YAML → SystemConfig + AgentRegistry (all agents)
    system_config, agent_registry = self._load_config(args)

    # 3. Re-apply logging at YAML-specified level
    setup_logging(system_config.debug_level)

    # 4. Build Backend instance for every registered item
    backends = self._build_backends(registry.item_configs)

    # 5. Create ItemService (ProjectedContext) and DI-inject into ItemRegistry
    registry.items = {
        name: ItemService(backend)
        for name, backend in backends.items()
    }

    # 6. Build CliAdapter
    cli_adapter = self._create_cli_adapter()

    # 7. Hand off to ApplicationBase
    ApplicationBase(registry, cli_adapter).run()
```

**YAML-absent fallback**: when `config_path` is absent or the file does not
exist, continue with defaults. This allows `Bootstrap().run(ArgData())` to run
in tests without a YAML file.

### YAML schema (reference: `sample-project`)

```yaml
system:
  debug_level: debug
  work_dir: /tmp/<package>

items:
  default-item:
    backend: default
    model: ""
    base_url: ""
  alt-item:
    backend: alt
    model: ""
    base_url: ""
```

---

## 11. Naming Conventions

| Target | Rule | Example |
|--------|------|---------|
| Package name | `snake_case` | `sample_project` |
| Module name | `snake_case` | `core_model/type.py`, `agent_service.py` |
| CPA layer folder | CPA vocabulary as-is (`snake_case`) | `core_model/`, `projected_context/`, `application_base/`, `interface/`, `boot/` |
| Port folder | `port/` | `core_model/port/` |
| Protocol class name | `<Role>` (no suffix) | `Agent`, `AiBackend`, `CliAdapter` |
| UseCase class name | `<Domain>Service` | `ItemService` |
| dataclass type name | `PascalCase` | `ItemConfig`, `ItemRegistry`, `ArgData` |
| Constants | `_ALL_CAPS` | `_BACKEND_DEFAULT`, `_DEFAULT_DEBUG_LEVEL` |
| Logger acquisition | `get_logger(__name__)` in every module | — |

---

## 12. Testing — pyproject.toml + Stub Pattern

### pyproject.toml setup

```toml
[tool.pytest.ini_options]
pythonpath = ["src"]
```

This allows `pytest` to run without `pip install -e .`.

### Stub pattern for cross-layer tests

Use minimal classes that structurally satisfy a Protocol — no import from the
Protocol module itself is required.

```python
# tests/.../application_base/test_control.py

class _StubItem:
    def process(self, user_input: str) -> str:
        return "stub_reply"

class _StubCliAdapter:
    def prompt_input(self, message: str) -> str: return "hello"
    def display_output(self, message: str) -> None: pass
    def display_error(self, message: str) -> None: pass

def test_run_calls_chat_and_display():
    registry = ItemRegistry(
        item_configs={"a": ItemConfig(backend="default", model="", base_url="")},
        active_item_name="a",
        items={"a": _StubItem()},
    )
    cli = _StubCliAdapter()
    app = ApplicationBase(registry, cli)
    app.run()   # must not raise
```

### Integration test pattern

```python
# tests/.../boot/test_bootstrap.py
from sample_project.boot.bootstrap import Bootstrap
from sample_project.core_model.type import ArgData

def test_bootstrap_run_no_yaml():
    """Bootstrap must complete without error when no YAML is present."""
    Bootstrap().run(ArgData())
```

---

## 13. Build / Test Commands

These are the Python defaults for `{{BUILD_COMMAND}}` and `{{TEST_COMMAND}}`
in `AGENTS.md`.

| Placeholder | Default value | Notes |
|-------------|--------------|-------|
| `{{BUILD_COMMAND}}` | `python -m py_compile $(find src -name "*.py")` | Syntax check only; no compilation step in Python |
| `{{TEST_COMMAND}}` | `python -m pytest` | Requires `pythonpath = ["src"]` in pyproject.toml |
| `{{RUN_COMMAND}}` | `python -m <package_name>` | Standard module entry point |

Common variants:

```bash
# Run all tests
python -m pytest

# Run with verbose output
python -m pytest -v

# Run specific layer tests
python -m pytest tests/<pkg>/application_base/

# Run the application
python -m <package_name> -y config.yaml --agent openai-agent
```
