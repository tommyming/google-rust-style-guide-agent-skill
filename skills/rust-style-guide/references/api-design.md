---
description: "API design rules for functions, types, traits, and error reporting from Google Rust Style Guide."
connections: [organization, naming-docs-impl]
---

# API Design

## Function Inputs and Outputs

### Accept Flexible References for Immutable Data

Prefer `&T`, `&[T]`, `&str`, `Option<&T>` over `&Box<T>`, `&Vec<T>`, `&String`, `&Option<T>`.

### Iterators vs. Owned Collections in Return Types

In libraries, prefer returning `impl Iterator` over `Vec<T>`. In application code, return `Vec<T>` if simpler.

### Iterators vs. Slices in Function Parameters

In libraries, accept `impl IntoIterator`. In application code, use `&[T]` if simpler.

### Accepting `Into`

Use `impl Into<T>` in library APIs for call-site ergonomics (balance against code bloat).

### Prefer `enum` Over `bool` in APIs

Use `enum` when parameter purpose isn’t obvious. Document `bool` parameters with comments if used.

### Reporting Errors

- `Option`: Obvious, anticipated failures (e.g., missing key).
- `Result`: Recoverable errors needing context.
- `panic!`: Bugs or violated invariants (document why). Some codebases avoid panics entirely.

Exceptions: Developer-facing binaries and tests may panic more freely.

## Types

### Newtypes versus Type Aliases

Use newtypes (not aliases) for:

- Notable semantic differences
- Types warranting their own API
- Flexibility to change underlying type later
- Type safety (e.g., distinguish Day/Month/Year)

### Prefer Named Fields

Avoid tuples and non-newtype tuple structs. Named fields are clearer. Exceptions: newtype tuples, very local contexts.

## Traits

### Add Trait Implementations When Necessary

Proactive in low-level libraries; as-needed in application code.

### Implement Standard Library Traits

Prefer `impl FromStr for Foo` over custom `from_str()`. Both is acceptable for discoverability. See Clippy `should_implement_trait`.

### Use `#[derive()]` If Possible

Avoid manual impls when derivable.

### Generics versus `dyn Trait`

Prefer generics (`T: Trait` or `impl Trait`) in libraries. Use `dyn Trait` only when code size matters (extra indirection, less optimization). Some traits are not dyn-compatible.

### When to Define a Trait versus Inherent Implementation

Define a trait only to:

- Enable generic programming (`T: Trait`)
- Accept/return `impl Trait` or `dyn Trait`
- Define an extension trait

Otherwise, use inherent impls. Don’t use traits just to organize code.

### Extension Traits

Add methods to foreign types. Name with `~Ext` suffix (e.g., `ForeignTypeExt`). Owned by the team owning the type.

Sole purpose is adding methods; not for generics or dyn. Exceptions for well-known foundational extensions.

### Specific Guidance for Important Traits

- Public fields: Consider traits implemented by all subfields.
- Error types: Implement `Copy` if possible.
- `Copy`: When reasonably clear it will remain Copy.
- `Clone`: Most types unless external resource.
- `Default`: If has `new()` or default state (see Clippy `new_without_default`).
- `PartialEq`/`Eq`: Prefer `PartialEq` for `==`; `Eq` requires reflexivity.
- `PartialOrd`/`Ord`: Prefer `PartialOrd` for `<`/`>`; `Ord` requires total order.
- Map keys: Implement `Hash` + `Eq`; `Ord` if total ordering exists.

### Pair Important `From` Impls with Constructors

If important `From` is a primary construction method, add inherent `::new()`, `::from_foo()`, or `::new_from_foo()`.

### Options Structs Should Implement `Default`

Public-field “bag of options” structs should implement `Default` for struct update syntax.