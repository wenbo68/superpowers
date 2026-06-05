# Dependency Overview

- Date: 2026/06/05
- Sources: `package.json` (root), `gemini-extension.json`, `tests/brainstorm-server/package.json`

> Notes: Superpowers is a **zero-dependency plugin by design** (see CLAUDE.md — PRs adding third-party
> deps are rejected unless they add a new harness). The root `package.json` declares **no** runtime or
> dev dependencies. The only third-party dependency in the repo lives in an isolated **test** manifest.

## List

### `package.json` (root) — runtime & dev dependencies

- _(none declared)_
    - Intentionally zero-dependency. The plugin ships as Markdown skills + shell/Node hooks with no
      npm runtime or dev dependencies. `main` points at `.opencode/plugins/superpowers.js`, but no
      external packages are required to run it.

### `tests/brainstorm-server/package.json` — `dependencies`

- `ws`: `^8.19.0`
    - Node WebSocket library, used only by the brainstorm-server test suite
      (`server.test.js` / `ws-protocol.test.js`) to exercise the visual-brainstorming companion
      server's WebSocket protocol. Not a dependency of the shipped plugin.

## Taxonomy

- Test-only / WebSocket: libraries pulled in solely to test the visual brainstorming companion server; not part of the distributed plugin.
    - `ws`
