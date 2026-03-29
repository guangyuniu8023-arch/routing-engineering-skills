# Routing Quality Engineering

TDD-based methodology for systematically improving LLM skill routing accuracy. Works with any planner + skill architecture where an LLM routes user intent to skill descriptions.

## What it does

Applies Test-Driven Development to skill routing: test cases come from **user intent** (not descriptions), descriptions are **production code** modified only to fix failing tests.

```
Phase 0 → Layer 1 → Layer 2 → Layer 3 → Layer 4
discover   golden    ambiguity  boundary   integration
```

Also includes a **fixing-routing** workflow for single bug hotfixes.

## Skills included

| Skill | Trigger | What it does |
|-------|---------|-------------|
| `/routing-layer1` | Starting routing improvement | Lock core scenarios with golden cases |
| `/routing-layer2` | Layer 1 complete | Resolve ambiguous inputs |
| `/routing-layer3` | Layer 2 complete | Arbitrate competing skill boundaries |
| `/routing-layer4` | Layer 3 complete | Full regression testing |
| `/fixing-routing` | Single routing bug found | Quick 6-phase diagnosis and fix |

## Installation

### Claude Code (plugin)

```bash
claude plugin install routing-quality
```

Or test locally:

```bash
claude --plugin-dir ./routing-quality-plugin
```

### Cursor

Copy skills into your Cursor skills directory:

```bash
cp -r skills/* .cursor/skills/
```

Then copy the `docs/` folder to your project root as `docs/routing-quality/`.

### Other tools (Gemini CLI, Codex CLI, etc.)

SKILL.md is an open standard ([agentskills.io](https://agentskills.io)). Copy the `skills/` directory to wherever your tool loads skills from, and place `docs/` in your project.

## Prerequisites

Your project needs:
- A **planner** (LLM that routes user intent to skills)
- **Skills** with `description` fields (the "production code" this methodology improves)
- A way to **run routing tests** (send input → check which skill was selected)

## Key principles

- **Iron Law**: Don't read description before writing tests. Don't modify description without a failing test.
- **Minimal change**: Only change what's needed to make failing cases pass.
- **REFACTOR**: After all green, clean up descriptions (deduplicate, check consistency, control length) while staying green.
- **Platform-agnostic**: Docs reference no specific tool. Works with Claude Code, Cursor, or any LLM coding assistant.

## Docs structure

```
docs/
  README.md                    # Overview + flow selection
  phase0-discovery.md          # Generate project config
  layer1-golden-cases.md       # Core scenario testing
  layer2-ambiguity-cases.md    # Ambiguous input resolution
  layer3-boundary-cases.md     # Competing skill arbitration
  layer4-integration.md        # Full regression
  fixing-routing.md            # Single bug hotfix
  references/
    root-cause-taxonomy.md     # A-F failure classification
    description-rules.md       # 4 rules for modifying descriptions
    templates.md               # Evidence cards, specs, snapshots
```

## License

MIT
