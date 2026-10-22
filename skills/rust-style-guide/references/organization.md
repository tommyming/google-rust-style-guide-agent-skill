---
description: "Code organization, crates, modules, and import rules from Google Rust Style Guide."
connections: [api-design, naming-docs-impl]
---

# Code Organization

## Prefer Small Crates [Chrome]

Prefer small crates, ideally one file focusing on one abstraction. Corresponds to one `rust_static_library`. Larger crates only for trait coherence or foundational libraries.

Root can re-export from leaf modules. Evaluate if APIs belong together before using submodules.

**Rationale**: Better build graph, parallel compilation, caching in large codebases.

## Flat File Layout for `rust_library` Sources [Chrome]

All `.rs` files for one crate in a single directory. No subdirectories. If needed, split the crate.

Exception: `//third_party` built with Cargo uses conventional layout.

## Name Source Files and Leaf Modules

Name after the primary user-defined item (e.g., primary type `Foo` → `foo.rs` / `mod foo`).

Leaf modules without a primary item can be named flexibly.

## Don’t Implement APIs in Non-Leaf Modules

Non-leaf modules: only module declarations and re-exports. Small “ties things together” code is acceptable but keep minimal.

Leaf modules can contain `mod test` and binding modules like `#[cxx::bridge]`.

## Don’t Use Glob Imports

Avoid `use ...::*` except:

- `use super::*` for inline modules
- `use Enum::*` in enum method bodies or functions primarily dealing with that enum
- `pub use submodule::*` for non-leaf modules

C++ interop: Private glob merge of namespaces (e.g., merge `status::absl::*` and `strings::absl::*` into private `mod absl`).

Avoid defining preludes. Third-party preludes at reviewer discretion.

**Rationale**: Harder for LSCs and finding origins without xrefs.

## `use` Statements Import from One Module

Corresponds to `imports_granularity = Module`. No `::` inside `{...}` blocks.

```rust
// Good
use foo::bar::{Bar, Baz};

// Bad
use foo::{bar::Bar, baz::Baz};
```

## Prefer Locally-Scoped Trait Imports

Import traits inside function bodies unless used extensively throughout the file.

```rust
fn foo(x: u8) {
    use std::str::FromStr;
    let s: String = x.parse();
}
```

## Qualify Calls to Top-Level Functions

Call as `modulename::functionname()`. Import the module, not the function.

Exceptions: Disambiguated names (e.g., C++), small first-party crates in Chromium, collision-prone module names.

**Rationale**: Unqualified lowercase names assumed local for consistency.

## Type Names Should Be Imported and Used Unqualified

Use `Regex` after `use regex::Regex`, not `regex::Regex`.

Exceptions: Ambiguous common names (e.g., `io::Error` or alias), large type groups, macros/generated code, heavy dependency files.

## Use Appropriate Import Mechanism [Chrome]

Use `chromium::import!()` in Chrome.