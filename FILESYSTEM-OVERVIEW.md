# Filesystem Overview

- Date: 2026-06-05
- Summary: Superpowers is a multi-harness plugin that gives coding agents a complete software-development methodology built from composable, auto-triggering "skills" (brainstorming → spec → plan → subagent-driven TDD implementation → review). It ships as a zero-dependency plugin with parallel packaging for Claude Code, Codex, Cursor, OpenCode, Gemini, and Copilot; the bulk of the repo is Markdown skill content, shell hooks, and behavior tests rather than application code.

## Structure

```
superpowers/
├── .gitattributes            — Git attributes (line-ending / diff handling)
├── .gitignore                — Git ignore rules
├── .version-bump.json        — Declares which files/fields the version bumper updates and audit excludes
├── AGENTS.md                 — Contributor/agent guidelines for the Codex/AGENTS harness
├── CLAUDE.md                 — Contributor guidelines surfaced to Claude (strict PR rules, anti-slop policy)
├── CODE_OF_CONDUCT.md        — Project code of conduct
├── GEMINI.md                 — Contributor/context file for the Gemini CLI harness
├── LICENSE                   — MIT license
├── README.md                 — Project intro and per-harness installation instructions
├── RELEASE-NOTES.md          — Human-readable release notes / changelog
├── gemini-extension.json     — Gemini CLI extension manifest (name, version, context file)
├── package.json              — npm manifest; zero runtime deps, points main at the OpenCode plugin entry
├── .claude/                  — Claude Code agents, commands, and skills for this repo's own dev workflow
├── .claude-plugin/           — Claude Code plugin + marketplace manifests
├── .codex-plugin/            — Codex packaging manifest (synced copy of the plugin)
├── .cursor-plugin/           — Cursor packaging manifest
├── .git/                     — Git repository internals
├── .github/                  — Funding, issue templates, and the strict PR template
├── .opencode/                — OpenCode plugin entry point and install docs
├── assets/                   — Brand/icon assets
│   ├── app-icon.png          — Application icon
│   └── superpowers-small.svg — Small SVG logo
├── docs/                     — Design docs, plans, specs, and harness-specific notes
│   ├── README.opencode.md    — OpenCode-specific readme/notes
│   ├── testing.md            — How the test suites are organized and run
│   ├── plans/                — Dated implementation/design plan documents
│   │   ├── 2025-11-22-opencode-support-design.md          — OpenCode support design
│   │   ├── 2025-11-22-opencode-support-implementation.md  — OpenCode support implementation plan
│   │   ├── 2025-11-28-skills-improvements-from-user-feedback.md — Skill improvements from feedback
│   │   └── 2026-01-17-visual-brainstorming.md             — Visual brainstorming feature plan
│   ├── superpowers/          — Paired plans + specs for larger features
│   │   ├── plans/            — Dated implementation plans
│   │   │   ├── 2026-01-22-document-review-system.md
│   │   │   ├── 2026-02-19-visual-brainstorming-refactor.md
│   │   │   ├── 2026-03-11-zero-dep-brainstorm-server.md
│   │   │   ├── 2026-03-23-codex-app-compatibility.md
│   │   │   └── 2026-04-06-worktree-rototill.md
│   │   └── specs/            — Matching design specs for the plans above
│   │       ├── 2026-01-22-document-review-system-design.md
│   │       ├── 2026-02-19-visual-brainstorming-refactor-design.md
│   │       ├── 2026-03-11-zero-dep-brainstorm-server-design.md
│   │       ├── 2026-03-23-codex-app-compatibility-design.md
│   │       └── 2026-04-06-worktree-rototill-design.md
│   └── windows/              — Windows-specific implementation notes
│       └── polyglot-hooks.md — How hooks run cross-platform (bat/sh polyglot)
├── hooks/                    — Session-start hook that bootstraps Superpowers into a harness
│   ├── hooks-cursor.json     — Cursor hook registration
│   ├── hooks.json            — Claude Code SessionStart hook registration
│   ├── run-hook.cmd          — Cross-platform polyglot (cmd/bash) hook wrapper
│   └── session-start         — Bash script run at session start to load the bootstrap
├── scripts/                  — Maintenance/release scripts
│   ├── bump-version.sh       — Bumps version across all manifests per .version-bump.json
│   └── sync-to-codex-plugin.sh — Syncs plugin content into the .codex-plugin packaging
├── skills/                   — The core skill library (one folder per skill, each with SKILL.md)
│   ├── brainstorming/        — Turns a vague idea into a reviewed spec; includes a visual companion server
│   │   ├── SKILL.md          — Brainstorming skill instructions
│   │   ├── spec-document-reviewer-prompt.md — Subagent prompt for reviewing the produced spec
│   │   ├── visual-companion.md — Docs for the optional visual brainstorming UI
│   │   └── scripts/          — Local zero-dep server powering the visual companion
│   │       ├── frame-template.html — HTML frame served to the browser
│   │       ├── helper.js     — Client/helper JS for the visual companion
│   │       ├── server.cjs    — Node WebSocket/HTTP server (CommonJS)
│   │       ├── start-server.sh — Starts the companion server
│   │       └── stop-server.sh  — Stops the companion server
│   ├── dispatching-parallel-agents/        — Skill for fanning work out to parallel subagents
│   │   └── SKILL.md
│   ├── executing-plans/                    — Skill for executing an approved implementation plan
│   │   └── SKILL.md
│   ├── finishing-a-development-branch/      — Skill for wrapping up/merging a dev branch
│   │   └── SKILL.md
│   ├── receiving-code-review/              — Skill for acting on incoming code review feedback
│   │   └── SKILL.md
│   ├── requesting-code-review/            — Skill for requesting review from a reviewer subagent
│   │   ├── SKILL.md
│   │   └── code-reviewer.md  — Subagent prompt defining the code reviewer
│   ├── subagent-driven-development/        — Skill orchestrating implement/review subagent loop
│   │   ├── SKILL.md
│   │   ├── code-quality-reviewer-prompt.md — Reviewer subagent prompt (quality)
│   │   ├── implementer-prompt.md           — Implementer subagent prompt
│   │   └── spec-reviewer-prompt.md         — Reviewer subagent prompt (spec adherence)
│   ├── systematic-debugging/               — Skill for disciplined root-cause debugging
│   │   ├── CREATION-LOG.md   — Log of how this skill was developed/tuned
│   │   ├── SKILL.md
│   │   ├── condition-based-waiting-example.ts — Example of waiting on conditions not timers
│   │   ├── condition-based-waiting.md      — Reference on condition-based waiting
│   │   ├── defense-in-depth.md             — Reference on layered defenses
│   │   ├── find-polluter.sh                — Script to bisect a test-suite-polluting test
│   │   ├── root-cause-tracing.md           — Reference on tracing to root cause
│   │   ├── test-academic.md                — Pressure/eval test material
│   │   ├── test-pressure-1.md              — Adversarial pressure test
│   │   ├── test-pressure-2.md              — Adversarial pressure test
│   │   └── test-pressure-3.md              — Adversarial pressure test
│   ├── test-driven-development/            — Skill enforcing red/green TDD
│   │   ├── SKILL.md
│   │   └── testing-anti-patterns.md        — Reference listing testing anti-patterns to avoid
│   ├── using-git-worktrees/               — Skill for isolating work in git worktrees
│   │   └── SKILL.md
│   ├── using-superpowers/                 — Bootstrap skill that teaches the agent to use the library
│   │   ├── SKILL.md
│   │   └── references/       — Per-harness tool-name mappings
│   │       ├── codex-tools.md
│   │       ├── copilot-tools.md
│   │       └── gemini-tools.md
│   ├── verification-before-completion/     — Skill requiring verification before declaring done
│   │   └── SKILL.md
│   ├── writing-plans/                     — Skill for authoring implementation plans
│   │   ├── SKILL.md
│   │   └── plan-document-reviewer-prompt.md — Subagent prompt for reviewing a plan
│   └── writing-skills/                    — Meta-skill for authoring and testing new skills
│       ├── SKILL.md
│       ├── anthropic-best-practices.md     — Reference notes on skill-writing best practices
│       ├── graphviz-conventions.dot        — Graphviz conventions for skill diagrams
│       ├── persuasion-principles.md        — Reference on persuasion/behavior-shaping principles
│       ├── render-graphs.js                — Script to render the graphviz diagrams
│       ├── testing-skills-with-subagents.md — How to eval skills via subagents
│       └── examples/        — Example artifacts referenced by the skill
│           └── CLAUDE_MD_TESTING.md
└── tests/                    — Test suites validating skills, hooks, and harness integrations
    ├── brainstorm-server/    — Tests for the zero-dep visual brainstorming server
    │   ├── package-lock.json — Lockfile for the test's ws dependency
    │   ├── package.json      — Test manifest (depends on ws for WebSocket testing)
    │   ├── server.test.js    — Server behavior tests
    │   ├── windows-lifecycle.test.sh — Windows start/stop lifecycle test
    │   └── ws-protocol.test.js — WebSocket protocol tests
    ├── claude-code/          — Skill-behavior tests run against Claude Code
    │   ├── README.md         — How to run the Claude Code test suite
    │   ├── analyze-token-usage.py — Analyzes token usage from test runs
    │   ├── run-skill-tests.sh — Runs the skill test suite
    │   ├── test-document-review-system.sh
    │   ├── test-helpers.sh   — Shared shell test helpers
    │   ├── test-requesting-code-review.sh
    │   ├── test-subagent-driven-development-integration.sh
    │   ├── test-subagent-driven-development.sh
    │   └── test-worktree-native-preference.sh
    ├── codex-plugin-sync/    — Test that the codex-plugin sync script stays correct
    │   └── test-sync-to-codex-plugin.sh
    ├── explicit-skill-requests/ — Tests that explicit user requests trigger the right skills
    │   ├── run-all.sh
    │   ├── run-claude-describes-sdd.sh
    │   ├── run-extended-multiturn-test.sh
    │   ├── run-haiku-test.sh
    │   ├── run-multiturn-test.sh
    │   ├── run-test.sh
    │   └── prompts/          — Prompt fixtures driving the tests
    │       ├── action-oriented.txt
    │       ├── after-planning-flow.txt
    │       ├── claude-suggested-it.txt
    │       ├── i-know-what-sdd-means.txt
    │       ├── mid-conversation-execute-plan.txt
    │       ├── please-use-brainstorming.txt
    │       ├── skip-formalities.txt
    │       ├── subagent-driven-development-please.txt
    │       └── use-systematic-debugging.txt
    ├── opencode/             — Tests for the OpenCode plugin integration
    │   ├── run-tests.sh
    │   ├── setup.sh
    │   ├── test-bootstrap-caching.mjs
    │   ├── test-bootstrap-caching.sh
    │   ├── test-plugin-loading.sh
    │   ├── test-priority.sh
    │   └── test-tools.sh
    ├── skill-triggering/     — Tests that ambient prompts auto-trigger the right skills
    │   ├── run-all.sh
    │   ├── run-test.sh
    │   └── prompts/          — Prompt fixtures per skill
    │       ├── dispatching-parallel-agents.txt
    │       ├── executing-plans.txt
    │       ├── requesting-code-review.txt
    │       ├── systematic-debugging.txt
    │       ├── test-driven-development.txt
    │       └── writing-plans.txt
    └── subagent-driven-dev/  — End-to-end tests of the subagent-driven-development flow
        ├── run-test.sh
        ├── go-fractals/      — Sample project fixture (Go fractals)
        │   ├── design.md
        │   ├── plan.md
        │   └── scaffold.sh
        └── svelte-todo/      — Sample project fixture (Svelte todo app)
            ├── design.md
            ├── plan.md
            └── scaffold.sh
```
