---
description: "Naming, documentation, other features, concurrency, macros, and implementation concerns from Google Rust Style Guide."
connections: [organization, api-design]
---

# Naming, Documentation, Features & Implementation

## Naming

### Methods

Follow Rust std conventions. Use `as_`, `to_`, `into_` prefixes with correct `self` (see API Guidelines and Clippy `wrong_self_convention`).

### Lifetimes

Descriptive names, especially type lifetimes. Exceptions: extremely generic (e.g., `Cow`), anonymous `'_`, single-letter for local input/output relationships (up to two).

### Generic Parameters

Short, indicative names (single or multi-character). `T`/`U` fine for unbound contexts. Letter related to primary trait bound.

### Extension Traits

End with `~Ext` (e.g., `ForeignTypeExt`).

### Initialization Functions

- Constructors: `new()`, `new_with_foo()`, `try_new()` if returns `Result`.
- Value-to-value: `from_foo()` with associated `From` impl.

### Avoid Abbreviations

No acronyms/initialisms unlikely known outside project. Don’t delete letters in words. Exceptions: common Wikipedia abbreviations, unambiguous ones (e.g., `IpAddr`). See Google C++ Style Guide.

## Documentation

Document public APIs with `///` (items) and `//!` (modules/crates). Use Markdown. Consider examples, always document panics and unsafe invariants. Capitalization and punctuation.

Don’t explain language basics or write trivial comments that repeat the name. Omit if nothing insightful. Individual enum variants often omit if self-explanatory.

Mix `///`/`//!` with `//` for implementation notes.

Prefer direct doc comments over macro-generated ones for readability.

## Other Rust Features

### Unsafe Code

Only when no safe alternative. Encapsulate in safe abstraction (ideally separate low-dependency crate). Add `SAFETY:` comment explaining correctness. Provide technical justification (why not safe? performance benchmarks if relevant).

### No Unstable Features

Exceptions: Android Embedded (with review), Chrome style arbiters (small approved list), Fuchsia-specific.

### Getters and Setters

- `foo()`: getter by value
- `foo_mut()`: `&mut T`
- `take_foo()`: clear and return old
- `set_foo()`: setter by value
- `with_foo()` / `with_mut_foo()`: closure accessors (good for interior mutability or post-validation)
- `replace_foo()`: set and return old

### Implement Expected API Sets

If `len()`, also `is_empty()` (Clippy `len_without_is_empty`).

### Don’t Access `#[doc(hidden)]`

Treat as private unless you own the code.

### Use `const` Functions Responsibly

Only if expected to remain `const` (removing is breaking).

### Avoid Overloading for Non-Mathematical Purposes

Operators for math/logic only, not mere sugar.

### `#[non_exhaustive]`

On public enums and public-field structs used across teams. Forces wildcard matches / prevents external struct literals. Exceptions: never-changing ontologies, or committed to LSC fixes. See Clippy lints.

### `#[must_use]`

On functions whose return must not be ignored (pure functions, error/critical info). On types that are error types or lazy computations. `Result` already has it.

### Do Not Run Code Before `main()`

Avoid `ctor` crate or similar.

## Concurrency

- Never access poisoned mutexes directly.
- Don’t use `async`/`.await` [Chrome].
- Don’t start threads via std [Chrome]; use sequences.
- Use `fuchsia_sync::Mutex` [Fuchsia].

## Macros

Prefer in order: regular functions/types/traits/modules → `macro_rules!` → other code generation → `proc_macro`.

Macros hinder IDEs, Code Search, LSCs, and create dialects. Weigh costs carefully.

## Implementation Concerns

### Don’t Overuse Type Inference

Add explicit types so familiar readers can understand. Prefer on variable declarations over turbofish. Especially for `.into()`/`.parse()` and complex iterator chains.

### Prefer Pattern Matching Over Check-Then-Unwrap

Use `if let`/`match` to prove safety to the compiler instead of `is_some()` + `unwrap()`.

### Prefer `expect` Over `unwrap`

Document the invariant reason.

### Don’t Omit Patterns on `non_exhaustive` Enums

List variants or put unused in wildcard. Prefer `if let` for specific variants. Enable `non_exhaustive_omitted_patterns` lint.

### Use Context Strings in Assertions

Add format strings explaining why it should hold, logging values, or distinguishing API misuse vs. bug. Don’t just repeat the condition.

### Avoid Iterator Combinator Chains When Direct Iteration Is Clearer

Avoid long/complex chains, chains in `for` headers (assign to variable instead), and `for_each` unless justified. Prefer combinators for short, high-logic cases (e.g., `.any()`).

### Don’t Call `panic::catch_unwind`

Prohibited in first-party code (except defense-in-depth in unwind environments where panics are already minimized). Chrome/Android/Fuchsia use `panic=abort`.

### Prefer `format!` / `write!`

For concatenating >2 strings or substitutions (over `+` or `str::replace`). Fewer allocations, standard format.

## Tests

Use the codebase-specific framework (not raw `#[test]`):

- Chrome: `rust_gtest_interop`
- Fuchsia: `#[fuchsia::test]`