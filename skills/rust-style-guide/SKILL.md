---
name: rust-style-guide
description: "Apply Google's Rust Style Guide for consistent, readable, LSC-friendly Rust code. Use when writing, reviewing, refactoring, or answering questions about Rust style, API design, organization, naming, documentation, unsafe code, or implementation in Google codebases (Chrome, Android, Fuchsia, etc.)."
metadata:
  author: based-on-google-styleguide
  source: https://google.github.io/styleguide/rustguide.html
---

# Google Rust Style Guide

Apply the [Google Rust Style Guide](https://google.github.io/styleguide/rustguide.html) for consistency, readability, maintainability, and Large-Scale Change (LSC) friendliness. Follow official Rust style and API guidelines unless they conflict with Google rules.

Distinguish **library code** (API rules binding) from **application code** (strong recommendation; prefer simplicity over reusability).

## When to Use

- Writing new Rust code for Google codebases.
- Reviewing or refactoring existing Rust code.
- Designing APIs, crates, modules, traits, or error handling.
- Questions about naming, documentation, unsafe, macros, concurrency, or specific rules.

Always prefer official public Rust style (`rustfmt`, Rust API Guidelines) where no conflict exists. Reference the full guide for edge cases.

## Core Workflow

1. Identify if code is library or application.
2. Check code organization (crates, modules, imports).
3. Review function signatures (inputs/outputs, errors).
4. Validate types, traits, and implementations.
5. Apply naming, documentation, and feature-specific rules.
6. Check implementation concerns (inference, matching, iterators, etc.).
7. Note codebase-specific rules (marked [Chrome], [Fuchsia]).

For detailed rules, start at the knowledge graph: `references/INDEX.md`.

- Code organization & imports: `references/organization.md`
- Functions, types, traits, errors: `references/api-design.md`
- Naming, docs, features, implementation: `references/naming-docs-impl.md`

## Quick Key Rules

| Area | Rule | Notes |
|------|------|-------|
| Crates | Prefer small crates (ideally one file) [Chrome] | Split if too large |
| Files | Flat layout; name leaf modules after primary item | No subdirs for one crate |
| Imports | No globs (exceptions exist); one module per `use`; local trait imports; qualify top-level funcs | Types unqualified |
| Functions | Prefer `&T`/`&str`/`impl Into`; `enum` over `bool`; return iterators in libs | Errors: Option/Result/panic |
| Types | Newtypes for semantics/safety; named fields over tuples | |
| Traits | Derive when possible; generics over `dyn`; `~Ext` for extensions; implement std traits | |
| Naming | Descriptive lifetimes/generics; `new`/`try_new`/`from_`; avoid abbreviations | Methods: `as_`/`to_`/`into_` |
| Docs | `///`/`//!` for public APIs; document panics/invariants; Markdown | |
| Unsafe | Only if necessary; encapsulate; `SAFETY:` comments + justification | No unstable features |
| Impl | Explicit types for clarity; match over unwrap; `expect` over `unwrap`; avoid long combinator chains | Context in asserts |

## Codebase Notes

- **[Chrome]**: Small crates, flat files, no async/await, no std threads (use sequences), specific imports.
- **[Fuchsia]**: Use `fuchsia_sync::Mutex`.
- Test frameworks vary by codebase (e.g., `rust_gtest_interop` in Chrome).

For full details, examples, rationales, and exceptions, consult the references or the original guide. When generating or reviewing code, cite the specific rule and rationale where relevant.