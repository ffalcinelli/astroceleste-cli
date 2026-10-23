# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The command-line interface for the Astroceleste astrological calculation engine.

## Commands

```bash
cargo test                                                     # run all unit and integration tests
cargo fmt --all && cargo clippy --workspace --all-targets -- -D warnings
cargo doc --workspace --no-deps                                # CI runs with RUSTDOCFLAGS=-D warnings
cargo publish --dry-run                                        # test packaging

# Release preparation:
release-plz update                                             # bumps version & CHANGELOG from conventional commits
```

## Releasing

See `RELEASING.md`. Releases are trunk-based and trigger on `vX.Y.Z` git tags on `main`.
crates.io uses Trusted Publishing (OIDC) through `.github/workflows/release.yml`.
