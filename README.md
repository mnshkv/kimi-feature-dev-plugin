# kimi-feature-dev-plugin

**Guided 7-phase feature development for [Kimi Code](https://www.kimi.com/code) — explore the codebase, ask clarifying questions, design competing architectures, implement, and review with confidence-scored findings. Ported from Anthropic's feature-dev plugin for Claude Code.**

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-blue.svg)](LICENSE)
[![Platform: macOS / Windows / Linux](https://img.shields.io/badge/Platform-macOS%20%2F%20Windows%20%2F%20Linux-black.svg)]()
[![Kimi Code](https://img.shields.io/badge/Kimi%20Code-plugin-orange.svg)](https://www.kimi.com/code/docs/en/kimi-code-cli/customization/plugins.html)

## Why

Jumping straight into code produces features that fight the existing architecture. The original [feature-dev plugin](https://github.com/anthropics/claude-code-plugin-marketplace) (by Sid Bidasaria, Anthropic) embeds a disciplined workflow into the agent: understand the codebase deeply, resolve every ambiguity *before* designing, compare real architecture options, and only then implement and review.

This repo repackages that workflow for Kimi Code's plugin format with the prompts and agent definitions preserved.

## Features

| Area | What you can ask your agent to do |
|---|---|
| Full workflow | Run the complete 7-phase pipeline for a new feature |
| Codebase exploration | Dispatch parallel `code-explorer` agents to trace features, map architecture, list key files |
| Clarification | Get an organized list of questions about edge cases, scope, and requirements before any code is written |
| Architecture design | Compare minimal-change, clean-architecture, and pragmatic approaches designed by parallel `code-architect` agents |
| Quality review | Get confidence-scored findings (only ≥ 80/100 reported) from parallel `code-reviewer` agents |
| Standalone agents | Invoke `code-explorer`, `code-architect`, or `code-reviewer` individually for any task |

## Install

In any Kimi Code session:

```
/plugins install https://github.com/mnshkv/kimi-feature-dev-plugin
```

Then `/plugins reload` (or start a new session).

## Usage

**Full workflow** (recommended for anything non-trivial):

- "Run feature-dev: add user authentication with OAuth"
- "I need rate limiting on the API endpoints — run feature-dev"
- `/feature-dev`

The orchestrator skill walks you through all seven phases interactively and waits for your answers at each checkpoint (clarifying questions, architecture choice, pre-implementation approval, review findings).

**Standalone agents:**

- "Launch code-explorer to trace how authentication works"
- "Launch code-architect to design the caching layer"
- "Launch code-reviewer to check my recent changes"

### The 7 phases

1. **Discovery** — clarify the problem, constraints, and requirements; confirm understanding.
2. **Codebase Exploration** — 2-3 parallel `code-explorer` subagents trace similar features, map architecture, and return the 5-10 key files to read.
3. **Clarifying Questions** — every underspecified aspect (edge cases, error handling, backward compatibility) is asked in one organized list. Never skipped.
4. **Architecture Design** — 2-3 parallel `code-architect` subagents produce competing approaches (minimal / clean / pragmatic) with a recommendation; you pick.
5. **Implementation** — only after explicit approval; follows the chosen architecture and codebase conventions.
6. **Quality Review** — 3 parallel `code-reviewer` subagents (simplicity & DRY, bugs & correctness, conventions & abstractions) report only high-confidence issues; you decide what to fix.
7. **Summary** — what was built, key decisions, files modified, suggested next steps.

## How it works

```
You ──▶ feature-dev skill (orchestrator, 7 phases)
          ├─▶ Agent: code-explorer  ──▶ codebase analysis (Phase 2)
          ├─▶ Agent: code-architect ──▶ architecture blueprints (Phase 4)
          └─▶ Agent: code-reviewer  ──▶ confidence-scored review (Phase 6)
```

The orchestrator skill (`skills/feature-dev/SKILL.md`) controls the flow, asks you the questions, and dispatches the specialist subagents in `agents/` at the right phases. The agents also work standalone.

## Differences from the original

The upstream plugin is a Claude Code plugin; this port adapts the host-specific mechanics:

- **Subagent dispatch**: Claude's `Task` tool → Kimi Code's native Agent/subagent mechanism. The workflow is unchanged — agents still run in parallel with the same prompts.
- **Progress tracking**: Claude's `TodoWrite` → Kimi Code's built-in todo list (`TodoList`).
- **Slash command**: `/feature-dev` becomes the `feature-dev` skill; saying "run feature-dev" or describing a feature triggers the same pipeline.
- **Agent frontmatter**: Claude-specific fields (`model: sonnet`, `tools:` allow-lists, `color:`) are dropped; Kimi agents use `name` / `description` / `whenToUse` instead. Agent bodies are preserved.
- **Guidelines references**: `CLAUDE.md` mentions now cover `CLAUDE.md`/`AGENTS.md` or any equivalent project-guidelines file.
- Nothing else was omitted: there are no hooks, scripts, or `claude` CLI dependencies in the upstream plugin, so nothing was lost in the port.

## License & attribution

- Upstream: [anthropics/claude-code-plugin-marketplace](https://github.com/anthropics/claude-code-plugin-marketplace), plugin authored by Sid Bidasaria (Anthropic), licensed under [Apache-2.0](LICENSE).
- This port: plugin packaging © mnshkv, same Apache-2.0 license text included in this repo.
