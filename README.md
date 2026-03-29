# Routing Engineering

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

End-to-end engineering methodology for LLM skill routing systems. Guides you from **zero to production-quality routing** — scaffold the Agent, build each skill, then systematically improve routing accuracy with TDD.

Works with **any planner + skill architecture** where an LLM routes user intent to skill descriptions. Platform-agnostic: usable in Claude Code, Cursor, Windsurf, Gemini CLI, Codex CLI, or any tool that supports [SKILL.md](https://agentskills.io).

## Overview

```
/routing-setup → /routing-skill-build → /routing-layer1 → /routing-layer2 → /routing-layer3 → /routing-layer4
  scaffold        per-skill build        golden cases     ambiguity        boundary         integration
├──── Front half: Build ────┤├──────────── Back half: Quality ────────────┤
```

| Phase | Skills | What happens |
|-------|--------|-------------|
| **Build** | `/routing-setup`, `/routing-skill-build` | Scaffold Agent skeleton (9 architecture constraints), create SKILL.md + tools for each skill, generate routing test config |
| **Quality** | `/routing-layer1` ~ `/routing-layer4` | TDD-based 4-layer routing accuracy improvement (golden → ambiguity → boundary → integration) |
| **Hotfix** | `/fixing-routing` | 6-phase single-bug diagnosis and fix |

## Quick Start

### 1. Claude Code (Plugin mode)

```bash
# Clone the repo
git clone https://github.com/guangyuniu8023-arch/routing-engineering-skills.git

# Launch Claude Code with the plugin
claude --plugin-dir ./routing-engineering-skills
```

All 7 skills are immediately available as slash commands:

```
/routing-setup          # Start here if building from scratch
/routing-skill-build    # Fill in each skill
/routing-layer1         # Start here if you already have an Agent
/routing-layer2
/routing-layer3
/routing-layer4
/fixing-routing         # Hotfix a single routing bug
```

### 2. Cursor

```bash
git clone https://github.com/guangyuniu8023-arch/routing-engineering-skills.git

# Copy skills into Cursor's skill directory
cp -r routing-engineering-skills/skills/* .cursor/skills/

# Copy docs into your project (skills reference these docs)
cp -r routing-engineering-skills/docs your-project/docs/routing-quality/
```

Then type `/routing-setup` (or any skill name) in Cursor chat.

### 3. Windsurf

```bash
git clone https://github.com/guangyuniu8023-arch/routing-engineering-skills.git

# Copy skills and docs into your project
cp -r routing-engineering-skills/skills .windsurf/skills/
cp -r routing-engineering-skills/docs your-project/docs/routing-quality/
```

Use `/routing-setup` in Windsurf chat.

### 4. Gemini CLI

```bash
git clone https://github.com/guangyuniu8023-arch/routing-engineering-skills.git

# Copy into your Gemini skills directory
cp -r routing-engineering-skills/skills ~/.gemini/skills/
cp -r routing-engineering-skills/docs your-project/docs/routing-quality/
```

### 5. Codex CLI / Other AI tools

SKILL.md is an [open standard](https://agentskills.io). Copy `skills/` to wherever your tool loads skills from, and place `docs/` in your project:

```bash
git clone https://github.com/guangyuniu8023-arch/routing-engineering-skills.git

# Adapt paths to your tool
cp -r routing-engineering-skills/skills /path/to/your/tool/skills/
cp -r routing-engineering-skills/docs your-project/docs/routing-quality/
```

### 6. Manual (no tool integration)

You can use the methodology manually by reading the docs directly:

1. Read `docs/README.md` for the flow selection guide
2. Follow the relevant doc (e.g., `docs/routing-setup.md`) step by step
3. Apply the instructions to your project using any LLM assistant

## Usage

### Path A: Building from scratch

If you don't have an Agent yet, start here.

**Step 1 — Scaffold the Agent skeleton**

```
/routing-setup
```

What happens:
- **Phase A**: Collects your project goals, tech stack, LLM choice, and skill inventory
- **Phase B**: Generates a complete project structure following 9 architecture constraints (SkillStore 3-tier loading, Router Node, Plan/Replan nodes, Execute Service, Resource model, SSE events, Prompt templates)
- Creates a smoke test to verify the skeleton works

**Step 2 — Build each skill**

```
/routing-skill-build
```

For each skill in your inventory:
- Creates `skills/{name}/SKILL.md` with frontmatter (name, description, type, allowed_actions) and replan instructions
- Implements tool functions and registers them
- Runs a full-chain curl test (router → plan → load_skill → replan → execute)

After all skills are built:
- Auto-generates `.routing-quality/config.md` (Baseline Intent table)
- Generates `tests/routing_test_{N}skills.py` (3 golden cases per skill)
- Prompts you to continue with `/routing-layer1`

### Path B: Improving existing routing

If you already have a working Agent with skills, start here.

**Step 3 — Golden cases (Layer 1)**

```
/routing-layer1
```

- Auto-discovers your project structure (or runs Phase 0 if `.routing-quality/config.md` doesn't exist)
- Generates 3-5 golden test cases per skill from user intent (Baseline Intent)
- Runs tests, identifies failures, then reads descriptions to diagnose
- Iterates until >= 95% accuracy
- Saves snapshot to `.routing-quality/snapshots/v1-golden/`

**Step 4 — Ambiguity (Layer 2)**

```
/routing-layer2
```

- Builds ambiguity matrix (media type x intent type)
- Tests edge cases that could route to multiple skills
- Fixes descriptions → saves snapshot v2

**Step 5 — Boundary arbitration (Layer 3)**

```
/routing-layer3
```

- Identifies competing skill pairs (e.g., `image-editing` vs `image-generation`)
- Defines fallback rules for undecidable cases
- Tests boundary cases → saves snapshot v3

**Step 6 — Integration (Layer 4)**

```
/routing-layer4
```

- Merges all test cases from Layer 1-3
- Runs full regression (target: >= 95% overall, >= 98% golden)
- Generates final report

### Path C: Single bug hotfix

```
/fixing-routing
```

6-phase workflow: collect evidence → root cause (A-F taxonomy) → write spec → execute → test → record.

## Key Principles

- **Iron Law**: Don't read descriptions before writing tests. Don't modify descriptions without a failing test.
- **Minimal change**: Only change what's needed to make failing cases pass.
- **REFACTOR**: After all green, clean up descriptions while keeping tests green.
- **Platform-agnostic**: All docs are tool-independent. Works with any LLM coding assistant.

## Project Structure

```
routing-engineering-skills/
├── .claude-plugin/
│   └── plugin.json              # Claude Code plugin metadata
├── skills/
│   ├── routing-setup/           # Phase A+B: Agent skeleton scaffold
│   │   └── SKILL.md
│   ├── routing-skill-build/     # Phase C+D: Per-skill build + handoff
│   │   └── SKILL.md
│   ├── routing-layer1/          # Layer 1: Golden cases
│   │   └── SKILL.md
│   ├── routing-layer2/          # Layer 2: Ambiguity resolution
│   │   └── SKILL.md
│   ├── routing-layer3/          # Layer 3: Boundary arbitration
│   │   └── SKILL.md
│   ├── routing-layer4/          # Layer 4: Integration testing
│   │   └── SKILL.md
│   └── fixing-routing/          # Single bug hotfix
│       └── SKILL.md
├── docs/
│   ├── README.md                # Flow selection guide
│   ├── routing-setup.md         # 9 architecture constraints detail
│   ├── routing-skill-build.md   # Handoff protocol (7 items)
│   ├── phase0-discovery.md      # Project config + Baseline Intent
│   ├── layer1-golden-cases.md   # TDD: RED → GREEN → REFACTOR
│   ├── layer2-ambiguity-cases.md
│   ├── layer3-boundary-cases.md
│   ├── layer4-integration.md
│   ├── fixing-routing.md        # 6-phase hotfix
│   └── references/
│       ├── root-cause-taxonomy.md   # A-F failure classification
│       ├── description-rules.md     # 4 rules for description changes
│       └── templates.md             # Evidence cards, specs, snapshots
├── LICENSE
└── README.md                    # This file
```

## How It Works Under the Hood

Each SKILL.md file uses a standard format with YAML frontmatter + markdown body:

```yaml
---
name: routing-layer1                    # Lowercase-hyphen, used as identifier
description: "Use when starting..."     # Triggers skill selection
type: guide                             # Skill type
---

# Instructions for the AI assistant
...
```

When you type `/routing-layer1` in your AI tool:
1. The tool finds the matching SKILL.md by name
2. Loads the full content (frontmatter + body) into the AI's context
3. The AI follows the instructions in the body, referencing docs as needed

This is why the plugin works across different tools — it's just structured markdown that any LLM can follow.

## Requirements

- **Front half** (`/routing-setup`, `/routing-skill-build`): No prerequisites. Starts from zero.
- **Back half** (`/routing-layer1` ~ `/routing-layer4`, `/fixing-routing`): Your project needs:
  - A planner (LLM that routes user intent to skills)
  - Skills with `description` fields
  - A way to run routing tests

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-improvement`)
3. Make your changes
4. Submit a pull request

When adding or modifying skills:
- Keep SKILL.md body < 800 words
- Keep docs < 1000 words
- Follow the [SKILL.md standard](https://agentskills.io)
- Ensure cross-file terminology consistency (skill names use lowercase-hyphen, Resource type uses Chinese "图片"/"视频")

## License

[MIT](LICENSE)
