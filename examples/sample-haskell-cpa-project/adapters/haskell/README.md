# CPA Haskell Adapter

This document describes how Core Projection Architecture (CPA) layer boundaries
are enforced in a **Haskell** project. All content here is Haskell-specific.
Language-neutral CPA concepts belong to `docs/cpa-core-vocabulary.md`.

---

## 1. Scale Reference

Project scale determines which CPA construction pattern to use.

> **Scale definitions and the selection guide are maintained in
> `docs/cpa-project-profile.md`.** Do not duplicate them here.

This adapter describes how each scale maps to **Haskell-specific** module and
package structures. Use the selection guide in `docs/cpa-project-profile.md`
to choose a scale, then return here for implementation details.

| Scale | Haskell structure |
|-------|------------------|
| `small` | Single `.cabal` file. Four CPA layers expressed as **module name segments** under `src/`. |
| `medium` | Single `.cabal` file. Four layers as modules, plus `GlobalContext` injected from an external Boot package. |
| `large` | **Multi-package** fractal layout. `build-depends` enforces Retro-dependency at the package level. |

---

## 2. Layer-to-Module / Package Mapping

### small — single-library, module-level layers

```
src/
  <Prefix>/
    CoreModel/
      Type.hs          -- domain types, IOFunc DI record, AppContext alias
      Constant.hs      -- pure constants (optional)
    ProjectedContext/
      <UseCase>.hs     -- domain logic, pure or IO
    ApplicationBase/
      Control.hs       -- monad-stack unwrap, use-case runners
    Interface/
      FileIO.hs        -- concrete IO implementation of IOFunc
      StdIO.hs
    <Root>.hs          -- Boot: public API facade, assembles IOFunc, calls Control
```

Example module prefix: `AIAgent.DiffPatch.Unified`

```haskell
-- CoreModel: pure types and DI record
module AIAgent.DiffPatch.Unified.CoreModel.Type where

type AppContext a = ReaderT AppData (ExceptT String IO) a

data IOFunc = IOFunc
  { _readIOFunc  :: FilePath -> IO T.Text
  , _writeIOFunc :: FilePath -> T.Text -> IO ()
  }
```

### medium — single-package, module-level layers + non-functional concerns

```
src/
  <Prefix>/
    CoreModel/
      Type.hs          -- WorldStateContext alias, StateTransition GADT,
                       --   Event GADT, EventW existential
    ProjectedContext/
      Semantic.hs      -- Semantic Configuration logic
      Ontological.hs   -- Ontological Configuration logic
    ApplicationBase/
      Control.hs       -- conduit pipeline (src/work/sink), state-transition loop,
                       --   logger management, TH-generated transit functions
    Interface/
      ...              -- delegated to external packages (cpa-request, cpa-response)
```

`GlobalContext` (TQueue group) is injected from the Boot package and declared in
`CoreModel/Type.hs` as a `ReaderT` environment.

```haskell
-- CoreModel: monad stack, state machine, event types
module CPA.Fractal.CoreModel.Type where

type WorldStateContext =
  ExceptT String (StateT WorldStateW (ReaderT GlobalContext (LoggingT IO)))

data StateTransition
  = StartToSemantic
  | SemanticToOntological
  | OntologicalToSemantic
  deriving (Show, Eq, Ord, Enum, Bounded)

data Event r where
  ...

data EventW = forall r. EventW (Event r)
```

### large — multi-package fractal layout

```
CoreModel/
  cpa-multiverse/          -- shared CoreModel for all packages
ProjectedContext/
  cpa-semantic-world/      -- Logical / Semantic Configuration
  cpa-ontological-world/   -- Ontological Configuration
ApplicationBase/
  cpa-fractal-app/         -- orchestration (conduit pipeline, state machine)
Interface/
  cpa-request/             -- input Interface
  cpa-response/            -- output Interface
Boot/
  cpa-bootstrap/           -- DI, config loading, thread startup
```

Each package internally follows the same four-layer module structure
(**Fractal Recursion**). For example, `cpa-semantic-world` has its own
`CoreModel/`, `ProjectedContext/`, `ApplicationBase/`, and `Interface/` modules.

---

## 3. Retro-dependency Enforcement

In Haskell, dependency direction is enforced **statically** by the build system.

### Package-level (large)

`build-depends` in each `.cabal` file is the enforcement boundary:

```cabal
-- cpa-semantic-world.cabal
-- Allowed: depends on cpa-multiverse only
build-depends:
    cpa-multiverse
  , base

-- NOT allowed: must not depend on cpa-fractal-app, cpa-bootstrap, etc.
```

If an inner package accidentally imports an outer package, `cabal build` fails.
This makes Retro-dependency a **compile-time guarantee** for large-scale projects.

### Module-level (small / medium)

Within a single package, enforcement is by **import discipline** and code review.
Enable `-Wall` and `-Wmissing-signatures` in the `.cabal` file to catch accidental
exposures:

```cabal
ghc-options: -Wall -Wmissing-signatures
```

Violation pattern to avoid:

```haskell
-- BAD: CoreModel importing from ApplicationBase
module MyApp.CoreModel.Type where
import MyApp.ApplicationBase.Control (runSomething)  -- retro-dependency violation
```

---

## 4. ApplicationBase Patterns

ApplicationBase is where CPA scale differences are most visible. The pattern
grows from a minimal use-case runner (small) to a full orchestration engine
(large).

### small — use-case runner only

No state machine, no logging, no pipeline. ApplicationBase is only responsible
for unwrapping the monad stack and dispatching use-case functions.

```haskell
module AIAgent.DiffPatch.Unified.ApplicationBase.Control where

-- Unwrap the AppContext monad stack
runProjectedContext :: AppData -> AppContext a -> IO (Either String a)
runProjectedContext appData ctx = runExceptT (runReaderT ctx appData)

-- Use-case runners: receive IOFunc from Boot, call ProjectedContext
runPatch :: IOFunc -> FilePath -> String -> IO (Either String ())
runPatch ioFunc path patchText = runProjectedContext def $
  ProjectedContext.applyPatch ioFunc path patchText

runDiff :: IOFunc -> FilePath -> FilePath -> IO (Either String ())
runDiff ioFunc origPath newPath = runProjectedContext def $
  ProjectedContext.computeDiff ioFunc origPath newPath
```

### medium — conduit pipeline + state machine + logging

ApplicationBase wires the conduit pipeline (source → process → sink), manages
the state-transition loop, and controls logging. TemplateHaskell generates
transition-dispatch functions automatically.

```haskell
module CPA.Fractal.ApplicationBase.Control where

-- Entry point: receives GlobalContext from Boot
run :: GlobalContext -> IO ()
run ctx = runStdoutLoggingT $ runReaderT (runConduit pipeline) ctx

-- Conduit pipeline: source / process / sink stages
pipeline :: ConduitM () Void WorldStateContext ()
pipeline = source .| process .| sink

-- State-transition loop (TH-generated dispatch omitted for brevity)
transit :: StateTransition -> WorldStateContext ()
transit = $(funcTH_transit 'transitTable)
```

### large — fractal inter-package orchestration

ApplicationBase (`cpa-fractal-app`) orchestrates multiple worlds. Each world
package runs its own pipeline independently; `cpa-fractal-app` coordinates their
lifecycles through shared `GlobalContext` queues.

```
cpa-bootstrap  →  spawns threads for cpa-fractal-app
cpa-fractal-app → starts / monitors cpa-semantic-world, cpa-ontological-world
cpa-semantic-world  →  own conduit pipeline + state machine
cpa-ontological-world →  own conduit pipeline + state machine
```

---

## 5. Erosion Analysis

Erosion analysis determines whether the execution substrate belongs inside or
outside the CoreModel boundary. Haskell's type system makes erosion explicit
through the monad stack.

### Semantic / Logical Configuration

The execution substrate (IO, network, file system) is a **delivery mechanism**.
CoreModel types remain valid if the substrate is removed.

Haskell pattern: CoreModel types are substrate-free; effects are lifted at the
ApplicationBase boundary.

```haskell
-- CoreModel: pure domain type (no IO)
data DiffResult = DiffResult
  { _hunksResult :: [Hunk]
  } deriving (Show, Eq)

-- ProjectedContext: may use IO via IOFunc DI, but no concrete substrate type
computeDiff :: IOFunc -> FilePath -> FilePath -> AppContext DiffResult

-- ApplicationBase: unwraps IO and calls ProjectedContext
runDiff :: IOFunc -> FilePath -> FilePath -> IO (Either String DiffResult)
```

Suitable for: API libraries, CLI tools, MCP servers, batch processors, protocol
adapters.

### Ontological Configuration

The execution substrate **defines conditions of existence** for CoreModel.
Removing the substrate would erase or fundamentally change the Core.

Haskell pattern: CoreModel types are parameterised by or embed execution-world
types (world identity, lifecycle, real-time context).

```haskell
-- CoreModel: world-parameterised type (substrate is part of existence)
data WorldStateW = WorldStateW
  { _stateWorldStateW      :: StateTransition
  , _eventQueueWorldStateW :: TQueue EventW   -- existence depends on STM
  }

-- Monad stack: StateT carries WorldStateW as part of Core existence
type WorldStateContext =
  ExceptT String (StateT WorldStateW (ReaderT GlobalContext (LoggingT IO)))
```

Suitable for: game engines, real-time simulations, event-loop–driven services,
systems where lifecycle and identity are conditions of existence.

### Erosion Decision Guide (Haskell)

```
1. Can CoreModel types be defined without importing any IO / STM / async type?
   → Yes: Semantic Configuration (keep CoreModel pure).

2. Does the domain concept only make sense within a running execution context
   (e.g. a live TQueue, an active game world, a real-time clock)?
   → Yes: Ontological Configuration (substrate is part of Core existence).

3. Is an IO or STM type appearing in CoreModel only for convenience
   (could be replaced by a pure abstract type)?
   → Move to Interface or inject via IOFunc / DI record; keep CoreModel pure.
```

> Retro-dependency still holds in both configurations. Neither configuration
> permits CoreModel to import Interface or Boot.

---

## 6. Naming Conventions

### Module names

CPA layer names are used as module name segments:

```
<OrgPrefix>.<ProjectName>.CoreModel.<Module>
<OrgPrefix>.<ProjectName>.ProjectedContext.<Module>
<OrgPrefix>.<ProjectName>.ApplicationBase.<Module>
<OrgPrefix>.<ProjectName>.Interface.<Module>
```

Boot is typically the top-level public facade module:

```
<OrgPrefix>.<ProjectName>          -- Boot / public API (small)
Boot.<ProjectName>.Bootstrap       -- Boot package entry (large)
```

### Type names

| Concept | Naming convention | Example |
|---------|------------------|---------|
| Application context alias | `<Scope>Context` | `AppContext`, `WorldStateContext` |
| Application data (ReaderT env) | `<Scope>Data` | `AppData`, `GlobalContext` |
| DI record for IO | `IOFunc` | `IOFunc` |
| State type for StateT | `<Scope>W` | `WorldStateW` |
| State machine enum | `StateTransition` | `StateTransition` |
| Event GADT | `Event r` | `Event r` |
| Existential event wrapper | `EventW` | `EventW` |

### Record field names

Follow the rule: `_` + camelCase meaning + PascalCase type name as suffix.

```haskell
data Hunk = Hunk
  { _oldStartHunk :: Int      -- _ + oldStart + Hunk
  , _oldCountHunk :: Int
  , _newStartHunk :: Int
  , _newCountHunk :: Int
  , _linesHunk    :: [Line]
  }
makeLenses ''Hunk
```

All record types must:
1. Use `makeLenses ''TypeName` (requires `TemplateHaskell` and `lens`).
2. Provide `instance Default` (requires `data-default`), or
   `default<TypeName> :: IO TypeName` when IO is needed for initialisation
   (e.g. `TMVar` fields).

### Constants

Constants (top-level value bindings equivalent to `const`) use the form
`_ALL_CAPS_WITH_UNDERSCORES`:

```haskell
_LF   :: T.Text
_LF   = "\n"

_CRLF :: T.Text
_CRLF = "\r\n"
```

---

## 7. Build / Test Commands

These are the Haskell defaults for `{{BUILD_COMMAND}}` and `{{TEST_COMMAND}}`
in `AGENTS.md`.

| Placeholder | Default value | Notes |
|-------------|--------------|-------|
| `{{BUILD_COMMAND}}` | `cabal build` | Use `cabal build all` for multi-package projects |
| `{{TEST_COMMAND}}` | `cabal test` | Use `cabal test all` for multi-package projects |

Override these in your project's `AGENTS.md` if the project uses Stack, Nix, or
a custom build script.

Common variants:

```bash
# Single package
cabal build
cabal test
cabal run <executable-name>

# Multi-package (large scale)
cabal build all
cabal test all

# With GHC options
cabal build --ghc-options="-Wall -Werror"
```

---

## 8. TemplateHaskell Usage

TemplateHaskell (TH) is used in two contexts in CPA Haskell projects.

### Lens generation (all scales)

Used in CoreModel record types to generate lens accessors automatically.

```haskell
{-# LANGUAGE TemplateHaskell #-}
module MyApp.CoreModel.Type where
import Control.Lens (makeLenses)

data AppData = AppData
  { _configAppData :: Config
  }

makeLenses ''AppData   -- generates: configAppData :: Lens' AppData Config
```

**Adoption criteria**: use in all projects (small / medium / large) whenever a
`data` record type is defined in CoreModel or any other layer.

### State-transition dispatch generation (medium / large)

Used in `ApplicationBase.Control` to generate the state-transition dispatch
function from a transition table, avoiding a large manual case expression.

```haskell
{-# LANGUAGE TemplateHaskell #-}
module CPA.Fractal.ApplicationBase.Control where
import CPA.TH (funcTH_transit)

transitTable :: [(StateTransition, WorldStateContext ())]
transitTable =
  [ (StartToSemantic,      transitStartToSemantic)
  , (SemanticToOntological, transitSemanticToOntological)
  ]

-- TH generates: transit :: StateTransition -> WorldStateContext ()
transit :: StateTransition -> WorldStateContext ()
transit = $(funcTH_transit 'transitTable)
```

**Adoption criteria**:
- `small`: not needed — ApplicationBase has no state machine.
- `medium`: recommended when `StateTransition` has 3 or more states.
- `large`: each package applies the same rule independently.

> Do not use TH outside `ApplicationBase`. Especially, CoreModel must remain
> TH-free except for `makeLenses`.
