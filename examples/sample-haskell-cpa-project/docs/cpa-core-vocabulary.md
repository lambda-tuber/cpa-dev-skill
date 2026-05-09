## CPA Core Vocabulary / Layer Responsibility Guide

### Core Thesis

Core Projection Architecture (CPA) is a structural design theory in which a system is understood as a manifestation process.
A discovered Core appears in the external world through contextual projection, and dependencies trace back in the reverse direction of that manifestation.

CPA is not merely a naming convention or a variant of layered architecture.
It provides a shared vocabulary for explaining why a concept belongs to a layer, why a dependency direction is valid, and how the execution world influences the structure of the Core.

### Why This Helps Human / AI Collaboration

CPA is useful as a shared foundation between humans and AI agents because it turns architectural judgment into explicit vocabulary.
Instead of relying on implicit taste such as "this feels like domain logic" or "this should be application logic," humans and agents can ask the same questions:

- What is the Core that must be discovered?
- Is this item a projection of the Core, or is it adding structure that should have been discovered in the Core?
- Does this dependency follow the reverse direction of manifestation?
- Is this execution-world influence Outside-in Erosion or Inside-out Erosion?
- Is this concern part of the generic CPA core, or is it a language/tool adapter detail?

This makes the architecture inspectable, discussable, and correctable during AI-assisted development.

---

### Discovery Principle

Definition:
The structural center of a system is not invented by the designer. It is discovered as the essence of the domain or as the operational characteristics of the execution world.

Responsibility / Role:
This principle guides the search for the Core Model. Design begins by recognizing what exists independently of a particular requirement, interface, or implementation strategy.

Do:
- Ask what remains valid when a specific use case, UI, transport, or runtime mechanism is removed.
- Treat Core discovery as recognition, not generation.
- Revise the Core when projections start introducing concepts that should have existed earlier.

Do not:
- Derive the Core only from an upper-layer API shape.
- Treat requirements enumeration as a substitute for Core discovery.

Adapter note:
Language-specific mechanisms for representing discovered structure belong to adapters. Examples include Haskell `data` / `newtype`, TypeScript interfaces, Python dataclasses, or database schema types.

### Projection Principle

Definition:
Projection is the operation that applies a purpose, requirement, or constraint to the Core and extracts a specific slice from its space of possibilities.

Responsibility / Role:
This principle defines the meaning of Projected Context. A use case is not an independent structure. It is a contextual projection of the Core.

Do:
- Define Projected Contexts as responses to specific purposes or requirements.
- Check that each projection limits the Core's possibilities instead of adding new domain structure.
- Move newly introduced domain concepts back into Core discovery when needed.

Do not:
- Let use cases accumulate independent domain knowledge.
- Treat projection as a place to invent concepts that the Core does not contain.

Adapter note:
Concrete implementation forms such as services, handlers, functions, workflows, or monadic actions are adapter-level expressions of projection.

### Retro-dependency Principle

Definition:
Dependencies are permitted only in the direction opposite to manifestation. A projection cannot exist without its source, but the source does not depend on the projection.

Responsibility / Role:
This principle explains why upper layers may depend on lower layers, while lower layers must remain independent of upper-layer concerns.

Do:
- Keep dependencies directed from external realization back toward the Core.
- Let Interface / Boot know concrete runtime details.
- Keep Core Model independent from Interface, Boot, infrastructure, and application orchestration.

Do not:
- Let the Core reference external interfaces or runtime implementations.
- Let Projected Context know how it will be executed by Interface or Boot.

Adapter note:
In statically typed languages this may be enforced by imports, modules, type signatures, or package boundaries. In dynamic languages it may require tests, linting, review rules, or explicit dependency policies.

---

### Core Model

Definition:
The Core Model is the formalization of discovered structure. It describes what exists.

Responsibility / Role:
Core Model holds stable concepts, invariants, relationships, and the minimal structure that remains meaningful independently of a particular interface or execution strategy.

Do:
- Place discovered domain concepts and invariants here.
- Keep it independent from upper-layer orchestration and external interfaces.
- Use it as the source from which projections are derived.

Do not:
- Put application workflows, transport details, persistence implementations, or UI concerns here.
- Add a concept to Projected Context if it is actually part of the discovered Core.

Adapter note:
The representation of Core Model depends on language and environment. The CPA core vocabulary does not require a specific type system.

### Projected Context

Definition:
Projected Context is a projection of the Core under a specific purpose, requirement, or constraint.

Responsibility / Role:
Projected Context defines functional meaning. It describes domain operations as contextual slices of the Core.

Do:
- Place requirement-specific transformations and operations here.
- Ensure each operation is grounded in concepts that exist in the Core.
- Represent effects if needed, while keeping execution of effects outside this layer when the configuration allows it.

Do not:
- Treat Projected Context as an independent application layer with its own domain model.
- Let it depend on Interface or Boot details.

Adapter note:
In some environments this layer may be pure. In others it may include an execution-world computational context. That distinction should be explained through erosion analysis, not by an absolute purity rule.

### Application Base

Definition:
Application Base is the control mechanism that brings a projection down to an execution context.

Responsibility / Role:
Application Base owns orchestration and non-functional control concerns such as runtime context, application state, lifecycle, configuration distribution, logging policy, error boundary, and execution runners.

Do:
- Place application-level control and orchestration here.
- Run or interpret Projected Contexts here.
- Manage application state and lifecycle when they are part of application operation rather than domain existence.

Do not:
- Put discovered domain concepts here.
- Put external interface conversion here when it belongs at the boundary.

Adapter note:
Concrete mechanisms such as monad transformer stacks, middleware pipelines, service containers, dependency graphs, or runtime contexts are adapter/tool expressions of Application Base.

### Interface

Definition:
Interface is the boundary with the external world where projections are realized as phenomena.

Responsibility / Role:
Interface converts external input into internal requests and converts internal results into external output. It is the layer where side effects are executed or externally observed.

Do:
- Place protocol, transport, rendering, persistence boundary, CLI, API, UI, or message conversion here.
- Keep Interface as a boundary adapter that invokes Application Base or Projected Context through the permitted dependency direction.

Do not:
- Place Core discovery or domain invariants here.
- Let Interface details leak into Core Model.

Adapter note:
The shape of Interface depends heavily on platform and tool. Examples include HTTP handlers, CLI commands, queue consumers, file-system adapters, UI views, or MCP tool handlers.

### Boot

Definition:
Boot is the startup and composition layer that creates runtime configuration, selects concrete implementations, and starts the system.

Responsibility / Role:
Boot knows the most concrete startup details. It performs dependency injection, reads configuration, constructs runtime values, and connects external implementations to the application.

Do:
- Place startup, configuration loading, wiring, dependency injection, and process/thread startup here.
- Keep concrete runtime selection outside the Core and Projected Context.

Do not:
- Put domain logic here.
- Let lower layers know which concrete implementation Boot selected.

Adapter note:
Boot may be explicit as a separate layer in implementation templates. In the four-layer theoretical view, startup concerns may be discussed around Interface/Application Base, but implementation guidance should treat Boot explicitly when wiring matters.

---

### Manifestation Direction

Definition:
Manifestation Direction is the causal direction in which discovered structure becomes externally realized.

Typical form:
Core Model -> Projected Context -> Application Base -> Interface -> external phenomenon.

Role:
It explains how meaning becomes concrete.

### Dependency Direction

Definition:
Dependency Direction is the permitted direction in which software elements refer to one another.

Typical form:
Boot / Interface -> Application Base -> Projected Context -> Core Model.

Role:
It is the spatial expression of the Retro-dependency Principle.

### Inward Dependency

Definition:
Inward Dependency means that outer, more concrete layers may depend on inner, more fundamental layers, while inner layers remain independent of outer layers.

Role:
It allows concrete execution to use discovered structure without making the discovered structure depend on execution details.

---

### Ontological Erosion

Definition:
Ontological Erosion is the phenomenon in which the operational characteristics of the execution world influence the form in which the Core Model is discovered.

Responsibility / Role:
Erosion analysis determines whether the Core can remain independent from the execution substrate or must include execution-world conditions as part of its existence.

Do:
- Treat erosion as an analysis target, not automatically as a failure.
- Ask whether the Core remains valid if the execution substrate is removed.
- Preserve Retro-dependency even when erosion is accepted.

Do not:
- Use erosion as an excuse to reverse dependency direction.
- Assume all execution-world influence is contamination.

### Logical Configuration / Semantic Configuration

Definition:
A configuration in which the execution substrate functions as a delivery mechanism and the Core Model remains valid if that substrate is removed.

Responsibility / Role:
Use this when the execution world transports, stores, displays, or invokes the Core without defining the Core's conditions of existence.

Typical examples:
Web applications, CLI tools, MCP servers, batch jobs, and protocol adapters where transport does not define the domain concepts.

Adapter note:
Appendix B expresses this with Haskell type signatures, but the concept is language-independent.

### Ontological Configuration

Definition:
A configuration in which the execution substrate defines conditions of existence for the Core Model.

Responsibility / Role:
Use this when removing the execution world would erase or fundamentally change what the Core is.

Typical examples:
Game engines, physical simulation environments, spatial worlds, real-time engine lifecycles, or frameworks where space, time, lifecycle, or entity identity are conditions of existence.

Adapter note:
Appendix B expresses this with world-parameterized types and computational contexts. Other languages should express the same idea using their own mechanisms.

### Outside-in Erosion

Definition:
Outside-in Erosion occurs when the execution substrate wraps the outside of the Core without entering the Core Model's definition.

Responsibility / Role:
It describes cases where execution concerns are absorbed by Interface and Application Base while the Core remains independent.

Do not:
- Confuse the mere presence of effects outside the Core with Inside-out Erosion.

### Inside-out Erosion

Definition:
Inside-out Erosion occurs when the execution world becomes part of the Core's conditions of existence.

Responsibility / Role:
It describes cases where the Core must be discovered with execution-world characteristics included.

Do not:
- Treat Inside-out Erosion as a dependency-direction exception. Retro-dependency still holds.

---

### Execution World Identification

Definition:
The first design question in CPA: determine whether the execution substrate is Delivery-type or Foundational-type.

Guiding question:
If the execution substrate is removed, do the concepts of the Core Model still hold?

- If yes, use Logical / Semantic Configuration.
- If no, use Ontological Configuration and determine the extent of erosion.

### Core Discovery

Definition:
The activity of identifying the minimal context-independent structure that the system is about.

Guiding question:
Does this concept hold even if a specific requirement, interface, transport, or execution strategy is removed?

### Projection Definition

Definition:
The activity of defining contextual slices of the Core for specific purposes, requirements, or constraints.

Guiding question:
Is this operation limiting the Core's possibility space, or is it introducing a concept that belongs in the Core?

### Projection Limitation

Definition:
The rule that a projection must not add new structure to the Core.

Role:
It is the practical design test for whether a Projected Context is valid or whether Core discovery is incomplete.

---

### Fractal Recursion

Definition:
Fractal Recursion is the property that the CPA layer composition can appear recursively at multiple autonomous scales.

Responsibility / Role:
It allows the same structural logic to apply to an entire system, a subsystem, a package, a module, or another autonomous design unit.

Do:
- Apply CPA structure at the scale where a unit has autonomous responsibility.
- Recognize that a component may be a layer in a larger system while also containing its own internal CPA structure.

Do not:
- Treat repeated implementation patterns alone as fractal structure.
- Confuse package/service boundaries with CPA layer boundaries.

### Autonomous Layer Composition

Definition:
An autonomous unit has its own meaningful Core, projections, control layer, interface, and sometimes boot/composition concerns.

Role:
It is the design condition under which the same CPA structure can recur inside a larger CPA structure.

### Scale-neutral CPA Unit

Definition:
A CPA unit is scale-neutral when the same principles can be applied regardless of whether the unit is a system, service, library, module, or component.

Role:
This supports the use of CPA as a general development skill rather than a framework tied to one language or project size.

---

### Adapter Boundary Notes

The following terms are not CPA core vocabulary. They are adapter/example vocabulary.

| Adapter term | Core concept it may express |
|--------------|-----------------------------|
| `ReaderT`, `ExceptT`, `StateT`, `LoggingT` | computational context / Application Base or Projected Context execution structure |
| `IO`, `liftIO`, `liftIOE` | side-effect boundary / effect lifting / execution-world interaction |
| `data`, `newtype`, `type class` | concrete representation of Core Model or execution-world abstraction |
| `TemplateHaskell`, `makeLenses`, `instance Default` | Haskell implementation convention |
| `cabal build`, `cabal test` | Haskell build/test command defaults |
| `pms-domain-model`, `pms-domain-service`, `pms-application-service`, `pty-mcp-server` | pty-mcp-server example mapping, not generic CPA vocabulary |

Core prompts should use language-neutral terms first.
Adapters may then show how those terms are expressed in a specific language, framework, or toolchain.

