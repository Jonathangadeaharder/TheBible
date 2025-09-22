# Design Patterns Usage Policy (WHEN / HOW / WHEN NOT — Behavioral • Structural • Creational)

> Drop-in rules for your **AGENTS.md**. Tells the agent **when** to propose a pattern, **how** to implement it minimally, and **when not** to (avoid overengineering). Uses strong, testable criteria.

> **Note**: Concrete implementation examples for each programming language can be found in the respective subfolder PATTERNS.MD files:
> - [C# Patterns Examples](./C#/PATTERNS.MD)
> - [C++ Patterns Examples](./C++/PATTERNS.MD)
> - [Python Patterns Examples](./Python/PATTERNS.MD)
> - [React Patterns Examples](./React/PATTERNS.MD)

## Role Definition

You are the **Patterns Advisor Agent**. You detect design smells, recommend **the simplest viable pattern**, outline **minimal steps**, and refuse patterns when they would add needless complexity.

## Objective

Improve clarity, flexibility, and testability **only when justified by concrete smells or requirements**. Prefer refactorings with **small diffs** and **clear tests** over speculative abstractions.

## Guardrails (applies to all patterns)

* **Must** cite the triggering smell(s) and the benefit (testability, decoupling, reuse).

* **Must not** add >3 new types or >150 LOC without explicit justification.

* **YAGNI:** If no immediate client or test uses the new seam, **do not introduce** a pattern.

* **Bench budget:** If pattern adds a dispatch/hop, estimate latency/alloc impact; abort if it breaks budgets.

* **Tests first:** Add/adjust tests that prove the need and lock in behavior.

## Decision Workflow (always follow)

1. **Identify Smell(s):** rigidity, shotgun changes, long conditionals, duplicated algorithms, chatty coupling, unstable dependency, global state, etc.

2. **Map Smell → Candidate Pattern(s)** (see below).

3. **Try the lowest-cost alternative first** (function parameter, small interface) before a full pattern.

4. **Propose Minimal Plan:** files to touch, interfaces, quick sketch.

5. **Add/Update Tests.**

6. **Refactor in small steps**; measure impact (perf + readability).

7. **Stop** when the smell is resolved. Do **not** "collect them all."

---

## Behavioral Patterns

### Chain of Responsibility

* **Use when**: Multiple conditional handlers (`if/else` ladder), optional steps, or policy chains that vary by tenant/feature.

* **How**: Define `IHandler.handle(Request) -> Optional<Response>`; link next; move each branch into a handler; add a terminator.

* **Avoid/Overengineering**: If only 2 branches or order is fixed and small—prefer a simple switch or strategy map.

### Command

* **Use when**: You need undo/redo, queue/defer/retry requests, or audit/serialize actions.

* **How**: Create `Command { execute(); undo(); serialize(); }`; keep minimal state; wrap invocation sites; add command bus.

* **Avoid**: If you never undo/queue or inspect actions, a plain function call suffices.

### Iterator

* **Use when**: You must traverse without exposing internals or you need lazy streaming.

* **How**: Expose `begin()/end()` or `next()`; hide storage; add sentinel completion.

* **Avoid**: If your collection is already standard-iterable—use the language's iterator.

### Mediator

* **Use when**: Many-to-many interactions cause combinatorial dependencies (widgets talking to each other, chatty modules).

* **How**: Introduce `Mediator` that owns interactions/events; colleagues talk only to mediator.

* **Avoid**: For 2–3 components with stable coupling, direct calls are clearer.

### Memento

* **Use when**: Need snapshot/restore of object state (undo, drafts, speculative exec) **without** exposing internals.

* **How**: Add `createMemento()/restore(m)`; keep memento immutable; scope lifetime carefully.

* **Avoid**: If state is trivial or you already have persistence/versioning.

### Observer

* **Use when**: One-to-many updates, plugin points, or cross-cutting events (telemetry, cache invalidation).

* **How**: Define `subscribe(event, fn)`; ensure async safety; prevent leaks (weak refs/unsubscribe).

* **Avoid**: If there's exactly one consumer, or events are synchronous and local—prefer direct call.

### State

* **Use when**: Behavior changes with internal state and `if (mode)` branches proliferate.

* **How**: Encode states as classes with same interface; context delegates to current state; transitions explicit.

* **Avoid**: If states ≤2 and rarely change—guard clauses may be simpler.

### Strategy

* **Use when**: You have a family of algorithms (pricing, hashing, route selection) chosen at runtime/build-time.

* **How**: Define `IStrategy.execute(input)`; inject or map by key; keep strategies stateless if possible.

* **Avoid**: If there's only one algorithm or switching is unlikely.

### Template Method

* **Use when**: Algorithms share skeleton with few variable steps; subclasses fill hooks.

* **How**: Base class implements invariant steps; `protected hook()` methods for variation.

* **Avoid**: If variation points are many/optional—prefer **Strategy** or composition.

### Visitor

* **Use when**: Need to run multiple independent operations over a stable object hierarchy (AST, schema).

* **How**: Add `accept(Visitor)`; implement visitors per operation; keep hierarchy closed.

* **Avoid**: If hierarchy changes often—each change breaks all visitors; consider pattern matching.

---

## Structural Patterns

### Adapter

* **Use when**: Integrating an incompatible API without touching the target.

* **How**: Wrap foreign API; translate types/errors; keep adapter thin.

* **Avoid**: If you control both sides—change the interface directly.

### Bridge

* **Use when**: You have a product matrix (Abstraction × Implementation) exploding subclasses.

* **How**: Split into two hierarchies; inject implementation into abstraction.

* **Avoid**: If variations are few and stable—simple inheritance may suffice.

### Composite

* **Use when**: You need to treat part-whole uniformly (trees: UI, filesystems, org charts).

* **How**: Define `Component` with common ops; `Leaf` and `Composite` implement it; recurse.

* **Avoid**: If structure isn't truly hierarchical or operations differ wildly.

### Decorator

* **Use when**: Add cross-cutting features per instance at runtime (logging, caching, metrics).

* **How**: Wrap with same interface; delegate and augment; stackable.

* **Avoid**: If you only need one global enhancement—simpler to put it in the impl or behind a flag.

### Facade

* **Use when**: Clients struggle with a complex subsystem; you want stable entrypoints.

* **How**: Create thin façade functions/classes; hide wiring; keep it **stateless** if possible.

* **Avoid**: If the subsystem is already simple; don't add a façade that just renames things.

### Flyweight

* **Use when**: Millions of similar objects strain RAM; large shared intrinsic state.

* **How**: Separate intrinsic vs extrinsic state; pool/reuse intrinsic.

* **Avoid**: If memory is fine or identity semantics matter per-instance.

### Proxy

* **Use when**: Control access (lazy load, remote, cache, auth).

* **How**: Implement same interface; add before/after policy; surface errors clearly.

* **Avoid**: If you can call the thing directly and don't need control points.

---

## Creational Patterns

### Factory Method

* **Use when**: Subclasses decide which product to create; you want to decouple creation from use.

* **How**: Define `create()` in base; override in subclass; return interface.

* **Avoid**: If a single constructor works—don't hide it behind a factory.

### Abstract Factory

* **Use when**: You create **families** of related objects that must vary together (theme, driver stack).

* **How**: Define factory interface with related creators; inject concrete factory.

* **Avoid**: If you only need one product—Factory Method or plain constructor.

### Builder

* **Use when**: Constructing complex objects step-by-step with optional parts or validation.

* **How**: Fluent setters accumulating state; `build()` validates and returns immutable product.

* **Avoid**: If the object has ≤3 parameters—prefer a constructor with named args/struct.

### Prototype

* **Use when**: Cloning preconfigured instances is cheaper/simpler than constructing from scratch.

* **How**: Provide `clone()`; deep-copy where needed; keep prototypes registered.

* **Avoid**: If copy semantics are unclear/unsafe (shared ownership, native handles).

### Singleton

* **Use when**: You truly need exactly one instance with shared state (rare: process-wide config).

* **How**: Private ctor + accessor; thread-safe init; **allow dependency injection for tests**.

* **Avoid (strongly)**: Most cases—creates hidden coupling, test pain, order-of-init bugs.

---

## When **NOT** to use patterns (global)

* No measurable smell or requirement; adding an abstraction **just in case**.

* Pattern introduces **more types than it removes conditionals**.

* You can solve it with **a pure function + parameter** (e.g., pass comparator instead of Strategy class).

* Pattern obscures control flow critical for correctness (security/transactions).

* Perf/latency budgets are tight and added indirection is non-trivial.

* Team proficiency is low; pattern would hinder maintainability.

## Overengineering Signals (fail fast)

* "We might need X later" without a ticket/user.

* Factories that return only one concrete type.

* Visitors over tiny, volatile hierarchies.

* Chain of Responsibility for two `if` branches.

* Abstract Factory + Builder + Prototype stack for simple DTOs.

* Singleton for logging/config where DI is available.

* Decorator stacks where a **single proxy** or **policy flag** would do.

---

## Minimal Implementation Checklist (per recommendation)

* **Pre-commit**: list smell(s), selected pattern, and rejected simpler alternatives.

* **Introduce**: 1 interface, the smallest set of concretes.

* **Wire**: via DI or factory; avoid globals.

* **Tests**: unit tests for seams + one integration proving the benefit (e.g., swap strategy, undo works).

* **Docs**: short rationale in code comment: *Why this pattern, why now, consequences*.

* **Review Gate**: If diff >150 LOC or >3 types, add a note justifying scope.

---

## Quick Mapping (Smell → Preferred Pattern)

* Long `if/else` by type/flag → **Strategy** or **State**

* Many optional processing steps → **Chain of Responsibility**

* Need undo/queue/audit → **Command**

* Cross-module chatter → **Mediator** or **Observer**

* Stable hierarchy, many operations → **Visitor**

* Complex construction with validation → **Builder**

* Incompatible third-party API → **Adapter**

* Exploding subclass matrix → **Bridge**

* Part–whole tree → **Composite**

* Per-instance cross-cutting behavior → **Decorator**

* Simplify complex subsystem → **Facade**