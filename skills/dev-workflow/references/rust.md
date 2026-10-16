# Rust reference (dev-workflow)

Read this before writing or modifying Rust code.

## Toolchain

Look at: `Cargo.toml`, `rustfmt.toml`, `clippy.toml`, `rust-toolchain.toml`.
Follow the project; treat lints as errors.

- Check: `cargo fmt && cargo clippy --all-targets --all-features -- -D warnings`
- Test: `cargo test` (add `cargo nextest run` only if the project uses it).

## Ownership and data

- Take `&str`, `&[T]`, `&T` in parameters and return owned data only when
  needed; `impl AsRef<str>` / `impl Into<String>` for ergonomic APIs.
- `clone()` only when justified. `Rc<RefCell<_>>` / `Arc<Mutex<_>>` only when
  genuinely shared, not as a shortcut around the borrow checker.
- Types over strings: enums instead of stringly-typed flags, newtypes for domain
  ids (`struct OrderId(Uuid)`), `#[derive(Debug, Clone, PartialEq, Eq, Hash)]`
  where meaningful, `Default` only when the default is meaningful.

## Errors

- `Result<T, E>` with `?` propagation; `thiserror` for libraries and `anyhow` in
  binaries, following the project.
- **No `unwrap()` / `expect()` / `panic!()` in library or request paths.** If a
  call is truly infallible, justify it in a comment. No `Box<dyn Error>` in a
  public API when a real error type exists.
- No `unsafe` without a `// SAFETY:` comment stating the invariant. No
  `#[allow(...)]` without a reason comment - fix the lint instead.

## Idioms

- Iterators over index loops, `if let` / `let ... else`, exhaustive `match`, no
  needless `collect()` or allocation, `&Path` / `&OsStr` for paths.
- Async: never block in an async task (use the async fs/net APIs); be explicit
  about cancellation safety.
- Split a file past ~400 lines or when it mixes concerns: `src/foo.rs` plus its
  own `src/foo/` submodules.

## Docs

- `//!` module docs and `///` on every public item, with `# Errors`, `# Panics`
  and `# Examples` sections where applicable; `#[must_use]` on pure accessors and
  builders.

## Test

- Unit tests beside the code (`#[cfg(test)] mod tests`), integration tests in
  `tests/`, `#[tokio::test]` for async. Cover error paths, not only happy paths.
