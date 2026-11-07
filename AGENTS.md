# AGENTS.md

Guidance for coding agents working in this repository.

## What this is

`psst` is a small Rust *library* (not a binary) for CLI tools that need to store secrets. Given a group/application name, it stores secrets in a TOML file under that application's XDG data directory (e.g. `~/.local/share/<app>/psst.toml` on Linux). If a key already exists it returns it; otherwise it interactively prompts the user and remembers the reply.

The entire implementation is a single file: `src/lib.rs`. There are no binaries, examples, benches, or tests.

## Commands

- `cargo build` — build
- `cargo test` — run tests (currently there are none, but CI runs this)
- `cargo clippy` — CI runs clippy with `RUSTFLAGS: -Dwarnings`, so **any clippy warning fails CI**; run it locally before committing

CI (`.github/workflows/ci.yml`) runs build, test, and clippy on every push/PR to `main`.

## Conventions

- Rust edition **2015** (hence `extern crate` declarations in `src/lib.rs`; don't remove these or "modernize" the edition casually — bumping the edition would also require dropping those declarations together).
- Dependency versions use tilde requirements (`~0.4`) rather than caret; Dependabot updates them periodically. Keep the tilde style when adding/changing dependencies.
- Errors: `anyhow::Result` for the public API, with a custom `thiserror` error type (`NotStringError`) internally for typed errors.
- Public API surface is intentionally minimal: `psst::new(application) -> Result<PsstApplication>` and `PsstApplication::get(key) -> Result<String>`.
- Debug logging via the `log` crate macros (not println).

## Gotchas

- `PsstApplication::get` is interactive: if the key isn't already stored, it blocks on a terminal prompt (via `rprompt`). Keep this in mind if writing tests — there is no injection point for the prompt.
- Version bumps (`Bump version` in `Cargo.toml`) are done as standalone commits; see git history.
- Commit messages in history are short, lowercase-ish, imperative ("Bump version", "Fix warnings") — match that style.
