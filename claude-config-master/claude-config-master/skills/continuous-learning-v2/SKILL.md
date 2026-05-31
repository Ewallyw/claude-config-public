---
name: continuous-learning-v2
description: Instinct-based learning system — observes sessions, creates atomic instincts with confidence scoring, evolves them into skills/commands/agents. Powers /evolve, /instinct-*, /promote, /prune commands.
origin: ECC
version: 2.1.0
---

# Continuous Learning v2.1 — Instinct-Based Architecture

An advanced learning system that turns your Claude Code sessions into reusable knowledge through atomic "instincts" — small learned behaviors with confidence scoring.

## The Instinct Model

An instinct is a small learned behavior: one trigger, one action, confidence-weighted (0.3 = tentative, 0.9 = near certain), domain-tagged, and evidence-backed.

## How It Works

```
Session Activity → Hooks capture prompts + tool use
  → Observer agent analyzes patterns (background, Haiku)
  → Creates/updates instincts with confidence scores
  → /evolve clusters into skills/commands/agents
  → /promote elevates project instincts to global
```

## Quick Start

### 1. Initialize Directory Structure (auto on first use)

```bash
mkdir -p "${XDG_DATA_HOME:-$HOME/.local/share}/ecc-homunculus"/{instincts/{personal,inherited},evolved/{agents,skills,commands},projects}
```

### 2. Enable Observation Hooks (in settings.json)

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning-v2/hooks/observe.sh"
      }]
    }],
    "PostToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning-v2/hooks/observe.sh"
      }]
    }]
  }
}
```

### 3. Use Commands

| Command | Description |
|---------|-------------|
| `/instinct-status` | Show all instincts with confidence scores |
| `/evolve` | Cluster related instincts into skills/commands |
| `/instinct-export` | Export instincts to file |
| `/instinct-import <file>` | Import instincts from others |
| `/promote` | Promote project instincts to global scope |
| `/prune` | Remove stale low-confidence instincts |

## Confidence Scoring

| Score | Meaning |
|-------|---------|
| 0.3 | Tentative — suggested but not enforced |
| 0.5 | Moderate — applied when relevant |
| 0.7 | Strong — auto-approved for application |
| 0.9 | Near-certain — core behavior |

Confidence increases with repeated observation, decreases with contradiction.

## Scope Decision Guide

| Pattern Type | Scope |
|-------------|-------|
| Language/framework conventions | project |
| Code style, file structure | project |
| Security, general best practices | global |
| Git practices, tool workflows | global |

## Privacy

Observations stay local. Only instincts (patterns) can be exported — not raw observations. No code or conversation content is shared.
