# CPA Convergence — Haskell

This file provides the content for the **CPA Convergence** section of your
project's `AGENTS.md`. Copy the relevant sections into your project's `AGENTS.md`
and fill in the placeholders.

Only Haskell-specific details belong here. For CPA core vocabulary and
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

### CPA Layer Mapping (Haskell — small)

Scale: small
See docs/cpa-project-profile.md for the scale definition and selection guide.

| CPA Layer        | Module                                              | Notes                              |
|------------------|-----------------------------------------------------|------------------------------------|
| CoreModel        | {{MODULE_PREFIX}}.CoreModel.Type                   | Pure types, IOFunc DI record, AppContext alias |
| CoreModel        | {{MODULE_PREFIX}}.CoreModel.Constant               | Pure constants (if needed)         |
| ProjectedContext | {{MODULE_PREFIX}}.ProjectedContext.{{UseCase}}     | Domain logic, pure or IO via IOFunc |
| ApplicationBase  | {{MODULE_PREFIX}}.ApplicationBase.Control          | Monad-stack unwrap, use-case runners |
| Interface        | {{MODULE_PREFIX}}.Interface.FileIO                 | Concrete IO implementation of IOFunc |
| Interface        | {{MODULE_PREFIX}}.Interface.StdIO                  | (if needed)                        |
| Boot             | {{MODULE_PREFIX}}                                  | Public API facade; assembles IOFunc and calls Control |

Module prefix: `{{MODULE_PREFIX}}`
  Example: `AIAgent.DiffPatch.Unified`

### Build / Test Commands

- Build: `cabal build`
- Test:  `cabal test`

### Scale

- Scale: small
- Reason: {{REASON}}
  Example: Single library with no state machine, logging, or async pipeline.
           Use-case runner in ApplicationBase is sufficient.

### Type Stack Guide

```haskell
-- AppContext: ReaderT over ExceptT over IO
type AppContext a = ReaderT AppData (ExceptT String IO) a

-- IOFunc: DI record for all external IO
data IOFunc = IOFunc
  { _read{{IOFunc}}  :: FilePath -> IO T.Text
  , _write{{IOFunc}} :: FilePath -> T.Text -> IO ()
  }

-- AppData: ReaderT environment
data AppData = AppData
  { _{{field}}AppData :: {{FieldType}}
  }
```

### Erosion Analysis

- Configuration: Semantic (execution substrate is a delivery mechanism)
- CoreModel types are valid without IO substrate: Yes
- IO boundary: ApplicationBase.Control (via IOFunc injection from Boot)

Adjust this entry if Ontological Configuration is adopted after analysis.

### Naming Conventions

- Module prefix: `{{MODULE_PREFIX}}`
- Record field suffix rule: `_camelCaseMeaning<TypeName>`
  Example: `_oldStartHunk`, `_configAppData`
- Constants: `_ALL_CAPS` (e.g. `_LF`, `_CRLF`, `_MAX_SIZE`)
- All record types use `makeLenses` and `instance Default`
```

---

## Template: medium scale

```markdown
## CPA Convergence

### CPA Layer Mapping (Haskell — medium)

Scale: medium
See docs/cpa-project-profile.md for the scale definition and selection guide.

| CPA Layer        | Module                                              | Notes                              |
|------------------|-----------------------------------------------------|------------------------------------|
| CoreModel        | {{MODULE_PREFIX}}.CoreModel.Type                   | WorldStateContext alias, StateTransition GADT, Event GADT, EventW |
| ProjectedContext | {{MODULE_PREFIX}}.ProjectedContext.Semantic        | Semantic Configuration logic       |
| ProjectedContext | {{MODULE_PREFIX}}.ProjectedContext.Ontological     | Ontological Configuration logic    |
| ApplicationBase  | {{MODULE_PREFIX}}.ApplicationBase.Control          | Conduit pipeline, state-transition loop, logging, TH-generated transit |
| Interface / Boot | {{EXTERNAL_PACKAGE_REQUEST}}                       | Input Interface (external package) |
| Interface / Boot | {{EXTERNAL_PACKAGE_RESPONSE}}                      | Output Interface (external package)|
| Boot             | {{EXTERNAL_PACKAGE_BOOTSTRAP}}                     | DI, config, thread startup         |

Module prefix: `{{MODULE_PREFIX}}`
  Example: `CPA.Fractal`

### Build / Test Commands

- Build: `cabal build`
- Test:  `cabal test`

### Scale

- Scale: medium
- Reason: {{REASON}}
  Example: Single-package application with state machine (GADT transitions),
           structured logging (LoggingT), and conduit pipeline.

### Type Stack Guide

```haskell
-- WorldStateContext: full monad stack
type WorldStateContext =
  ExceptT String (StateT WorldStateW (ReaderT GlobalContext (LoggingT IO)))

-- WorldStateW: StateT payload
data WorldStateW = WorldStateW
  { _state{{WorldStateW}}    :: StateTransition
  , _eventQueue{{WorldStateW}} :: TQueue EventW
  }

-- GlobalContext: ReaderT environment (injected from Boot package)
data GlobalContext = GlobalContext
  { _{{field}}GlobalContext :: {{FieldType}}
  }

-- StateTransition: state machine states
data StateTransition
  = StartTo{{Next}}
  | {{State}}To{{State}}
  deriving (Show, Eq, Ord, Enum, Bounded)
```

### Erosion Analysis

- Configuration: {{Semantic | Ontological}}
- Reason: {{REASON}}
  Example (Ontological): WorldStateW embeds TQueue EventW; the domain concept
  only exists within a running STM runtime.

### Naming Conventions

- Module prefix: `{{MODULE_PREFIX}}`
- Record field suffix rule: `_camelCaseMeaning<TypeName>`
  Example: `_stateWorldStateW`, `_configGlobalContext`
- Constants: `_ALL_CAPS`
- All record types use `makeLenses` and `instance Default` (or `default<TypeName> :: IO TypeName` for TMVar fields)
- TemplateHaskell: `makeLenses` in CoreModel; `funcTH_transit` in ApplicationBase.Control
```

---

## Template: large scale

```markdown
## CPA Convergence

### CPA Layer Mapping (Haskell — large)

Scale: large
See docs/cpa-project-profile.md for the scale definition and selection guide.

| CPA Layer        | Package                         | Notes                                        |
|------------------|---------------------------------|----------------------------------------------|
| CoreModel        | {{cpa-multiverse}}              | Shared CoreModel; no dependency on other CPA packages |
| ProjectedContext | {{cpa-semantic-world}}          | Semantic Configuration; depends on CoreModel only |
| ProjectedContext | {{cpa-ontological-world}}       | Ontological Configuration; depends on CoreModel only |
| ApplicationBase  | {{cpa-fractal-app}}             | Orchestration pipeline; depends on ProjectedContext and CoreModel |
| Interface        | {{cpa-request}}                 | Input Interface                              |
| Interface        | {{cpa-response}}                | Output Interface                             |
| Boot             | {{cpa-bootstrap}}               | DI, config loading, thread startup; depends on all layers |

> Each package listed above internally uses the same four-layer module structure
> (Fractal Recursion). Replace `{{package-name}}` with your actual package names.

### Build / Test Commands

- Build: `cabal build all`
- Test:  `cabal test all`

### Scale

- Scale: large
- Reason: {{REASON}}
  Example: Multi-component system requiring independent deployment of Semantic and
           Ontological worlds; Retro-dependency must be enforced at the package level
           by build-depends.

### Type Stack Guide

Each package defines its own monad stack in its internal `CoreModel/Type.hs`.
The shared stack shape across worlds:

```haskell
-- Per-world monad stack (example from cpa-semantic-world)
type SemanticContext =
  ExceptT String (StateT {{WorldStateW}} (ReaderT GlobalContext (LoggingT IO)))

-- Shared GlobalContext (defined in cpa-multiverse)
data GlobalContext = GlobalContext
  { _{{field}}GlobalContext :: TQueue {{EventW}}
  , ...
  }
```

### Erosion Analysis

- Configuration: {{Semantic | Ontological | Mixed}}
- CoreModel (cpa-multiverse) depends on execution substrate: {{Yes | No}}
- Reason: {{REASON}}

Each world package may have a different configuration. Record the analysis
per package if needed.

### Naming Conventions

- Package naming: `<domain>-<layer-role>` (e.g. `cpa-multiverse`, `cpa-semantic-world`)
- Module prefix per package: `{{ORG}}.{{PackageRole}}` (e.g. `CPA.Multiverse`)
- Record field suffix rule: `_camelCaseMeaning<TypeName>`
- Constants: `_ALL_CAPS`
- All record types use `makeLenses` and `instance Default` (or `default<TypeName> :: IO TypeName`)
- TemplateHaskell: `makeLenses` in CoreModel; `funcTH_transit` in ApplicationBase per package
- Retro-dependency enforced by `build-depends` — inner packages must not list outer packages
```

---

## Reference

- Scale definitions and selection guide: `docs/cpa-project-profile.md`
- CPA layer vocabulary: `docs/cpa-core-vocabulary.md`
- Haskell implementation guide: `adapters/haskell/README.md`
- AGENTS.md template: `templates/AGENTS.md`
