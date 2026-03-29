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

## Prerequisites

Your project needs:
- A **planner** (LLM that routes user intent to skills)
- **Skills** with `description` fields (the "production code" this methodology improves)
- A way to **run routing tests** (send input, check which skill was selected)

## Installation

### Claude Code

```bash
git clone https://github.com/guangyuniu8023-arch/routing-quality-skills.git
claude --plugin-dir ./routing-quality-skills
```

After launching, skills are available as `/routing-layer1`, `/routing-layer2`, etc.

### Cursor

```bash
git clone https://github.com/guangyuniu8023-arch/routing-quality-skills.git
cp -r routing-quality-skills/skills/* .cursor/skills/
cp -r routing-quality-skills/docs your-project/docs/routing-quality
```

Then use `/routing-layer1` in Cursor chat.

### Gemini CLI / Codex CLI / Other tools

SKILL.md is an open standard ([agentskills.io](https://agentskills.io)). Copy `skills/` to wherever your tool loads skills from, and place `docs/` in your project.

## Usage

### Systematic improvement (Building flow)

Run skills in order. Each layer is one conversation session — results pass between layers via snapshot files.

**Step 1: Discovery**

```
/routing-layer1
```

The skill will first check for `.routing-quality/config.md`. If it doesn't exist, it runs Phase 0 automatically:
- Scans your project for skills, planner prompt, test files
- Asks you to define **Baseline Intent** for each skill (what should route here, what shouldn't)
- Saves config to `.routing-quality/config.md`

**Step 2: Golden cases (Layer 1)**

Still in `/routing-layer1`:
- Generates 3-5 golden test cases per skill from your Baseline Intent
- You review and confirm the cases
- Runs tests → identifies failures
- **Only now** reads skill descriptions to diagnose and fix failures
- Iterates until >= 95% accuracy
- Saves snapshot to `.routing-quality/snapshots/v1-golden/`

**Step 3: Ambiguity (Layer 2)**

New session:
```
/routing-layer2
```

- Loads Layer 1 snapshot + config
- Builds ambiguity matrix (media type × intent type)
- Tests edge cases that could go to multiple skills
- Fixes descriptions → saves snapshot v2

**Step 4: Boundary arbitration (Layer 3)**

New session:
```
/routing-layer3
```

- Identifies competing skill pairs (e.g., image-editing vs image-generation)
- Defines fallback rules for undecidable cases
- Tests boundary cases → fixes → saves snapshot v3

**Step 5: Integration (Layer 4)**

New session:
```
/routing-layer4
```

- Merges all test cases from Layer 1-3
- Runs full regression (target: >= 95% overall, >= 98% golden)
- Generates final report

### Single bug fix

When you find one routing bug (from logs, screenshots, or user reports):

```
/fixing-routing
```

6-phase workflow:
1. **Collect evidence** — standardize the bug into an evidence card
2. **Root cause** — classify using A-F taxonomy (coverage gap? competing attraction? schema conflict?)
3. **Write spec** — propose fix, check against 4 rules, get your approval
4. **Execute** — apply minimal description changes
5. **Test** — run regression, verify no new failures
6. **Record** — document the change for future reference

## Key principles

- **Iron Law**: Don't read description before writing tests. Don't modify description without a failing test.
- **Minimal change**: Only change what's needed to make failing cases pass. Don't rewrite untested areas.
- **REFACTOR**: After all green, clean up descriptions (deduplicate, check cross-skill consistency, control length) while keeping tests green.
- **Platform-agnostic**: All docs reference no specific tool. Works with any LLM coding assistant.

## Docs structure

```
docs/
  README.md                    # Overview + flow selection guide
  phase0-discovery.md          # Generate project config + Baseline Intent
  layer1-golden-cases.md       # Core scenario testing (TDD: RED → GREEN → REFACTOR)
  layer2-ambiguity-cases.md    # Ambiguous input resolution
  layer3-boundary-cases.md     # Competing skill boundary arbitration
  layer4-integration.md        # Full regression testing
  fixing-routing.md            # Single bug 6-phase hotfix
  references/
    root-cause-taxonomy.md     # A-F failure classification
    description-rules.md       # 4 rules + anti-patterns for description changes
    templates.md               # Evidence cards, specs, snapshot formats
```

## License

MIT
