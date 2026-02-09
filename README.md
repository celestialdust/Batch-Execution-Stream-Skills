# Batch Execution Stream Plugin

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/celestialdust/Batch-Execution-Stream-Skills)

A Claude Code plugin for orchestrating parallel agent work across multiple service boundaries with workstream-batch execution model.

## Overview

Execute multi-workstream projects by:
- **Workstream Decomposition:** Map service boundaries to isolated git worktrees
- **Dependency Modeling:** Organize tasks into batches, waves, and merge points
- **Smart Parallelization:** Dispatch 5-10+ agents simultaneously without conflicts
- **Visual Tracking:** ASCII dashboard for execution state visibility
- **Learning Feedback:** Systematic capture of insights back to project docs

## Built on Superpowers

This plugin extends the [Superpowers](https://github.com/obra/superpowers) skill framework by [@obra](https://github.com/obra), adding workstream-batch orchestration capabilities for large-scale parallel agent execution.

**Superpowers** teaches Claude Code foundational skills like brainstorming, test-driven development, systematic debugging, and subagent-driven development. This plugin builds on that foundation to handle complex multi-workstream projects with 5-10+ agents running in parallel.

**Key difference:** While Superpowers provides sequential and simple parallel execution patterns, this plugin adds:
- Workstream decomposition with git worktree isolation
- Batch-and-wave dependency modeling with merge points
- Dashboard-based execution tracking
- Systematic orchestration of existing superpowers skills

**Prerequisite:** Install [Superpowers](https://github.com/obra/superpowers) first, as this plugin orchestrates superpowers skills like `using-git-worktrees`, `dispatching-parallel-agents`, and `subagent-driven-development`.

## Plugin Structure

```
batch-execution-stream/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── commands/
│   └── workstream.md        # /workstream slash command
├── skills/
│   └── workstream-batch-execution/
│       ├── SKILL.md         # Main skill documentation
│       ├── dashboard-template.md    # ASCII dashboard template
│       └── integration-guide.md     # Skill orchestration reference
└── README.md
```

## When to Use

Use when executing approved design docs with:
- Multiple service boundaries (2+ independent services/modules)
- Complex task dependencies (parallel work, sequential work, convergence points)
- Need to coordinate 5-10+ parallel agents without conflicts
- Large implementation requiring orchestration

**Don't use when:**
- Single service/module (use `superpowers:subagent-driven-development`)
- Simple linear dependencies (use `superpowers:executing-plans`)
- Design not approved yet (use `superpowers:brainstorming` first)

## The Five-Phase Process

1. **Workstream Decomposition** - Map service boundaries → isolated worktrees
2. **Task Extraction & Dependency Modeling** - Create batches, waves, merge points
3. **Dashboard Setup** - Visual tracking with ASCII dashboard
4. **Batch Execution** - Smart parallelization using existing superpowers skills
5. **Learning Capture** - Feed insights back to CLAUDE.md

## Integration with Superpowers Skills

This skill orchestrates other superpowers skills:

| Phase | Calls Skill |
|-------|-------------|
| Workstream Decomposition | `superpowers:using-git-worktrees` (per workstream) |
| Parallel Batches | `superpowers:dispatching-parallel-agents` |
| Sequential Batches | `superpowers:subagent-driven-development` |
| Merge Points | `superpowers:requesting-code-review` |
| Final Integration | `superpowers:finishing-a-development-branch` |
| Each Task | `superpowers:test-driven-development` (via subagents) |

## Installation

### Prerequisites

Install [Superpowers](https://github.com/obra/superpowers) first. This plugin orchestrates superpowers skills, so they must be available.

```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

### Install This Plugin

**Step 1:** Register the marketplace:

```
/plugin marketplace add celestialdust/batch-execution-stream-marketplace
```

**Step 2:** Install the plugin:

```
/plugin install batch-execution-stream@batch-execution-stream-marketplace
```

**Step 3:** Verify:

```
/plugin list
```

You should see `batch-execution-stream` in the list.

## Usage

### Slash Command

```
/workstream
```

### Direct Skill Invocation

```
Use batch-execution-stream:workstream-batch-execution to orchestrate this implementation
```

## Standard Workflow with Superpowers

This plugin is designed as **Step 3** in a full development workflow using superpowers skills. Here's the end-to-end flow:

### Step 1: Brainstorm and Design

Use `superpowers:brainstorming` (or `/brainstorm`) to explore the feature, identify service boundaries, and produce an approved design doc.

```
/brainstorm

You: "I need to build a distributed task queue with a web dashboard and worker pool"
```

**Output:** Approved design doc with clear service boundaries identified.

### Step 2: Write Implementation Plan

Use `superpowers:writing-plans` (or `/write-plan`) to create a detailed, bite-sized implementation plan from the design doc.

```
/write-plan

You: "Write the implementation plan based on our approved design"
```

**Output:** Plan at `docs/plans/YYYY-MM-DD-<feature>.md` with exact files, commands, and expected outputs per task.

### Step 3: Execute with Workstream-Batch Orchestration

Use this plugin (or `/workstream`) to decompose the plan into parallel workstreams and execute.

```
/workstream

You: "Execute this plan using workstream-batch execution"
```

**What happens:**

```
┌─────────────────────────────────────────────────────────────┐
│ Controller (Single Claude Code Session)                     │
│                                                             │
│ Phase 1: Decompose plan into workstreams (WS-A, WS-B...)   │
│          Create git worktrees per workstream                │
│                                                             │
│ Phase 2: Model task dependencies into batches + merge points│
│                                                             │
│ Phase 3: Set up EXECUTION-DASHBOARD.md                      │
│                                                             │
│ Phase 4: Execute batches                                    │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                   │
│  │Subagent 1│ │Subagent 2│ │Subagent 3│  ← parallel       │
│  │Task A1   │ │Task B1   │ │Task E1   │                   │
│  │WS-A tree │ │WS-B tree │ │WS-E tree │                   │
│  └──────────┘ └──────────┘ └──────────┘                   │
│          ↓ verify ↓ verify ↓ verify                        │
│  Batch 1 ✅ → Dispatch Batch 2 → ... → Merge Point → ...  │
│                                                             │
│ Phase 5: Capture learnings back to CLAUDE.md                │
└─────────────────────────────────────────────────────────────┘
```

### Step 4: Finish and Ship

After all batches complete, `superpowers:finishing-a-development-branch` handles final integration, PR creation, and worktree cleanup.

### Complete Flow Summary

```
/brainstorm           → Design doc with service boundaries
        ↓
/write-plan           → Detailed implementation plan
        ↓
/workstream           → Parallel execution across workstreams
        ↓
finishing-a-branch    → PR, merge, cleanup
```

### When to Use Which

| Situation | Use |
|-----------|-----|
| New feature, unclear requirements | `/brainstorm` |
| Approved design, need implementation plan | `/write-plan` |
| Plan ready, 2+ service boundaries, complex deps | `/workstream` |
| Plan ready, single service, sequential tasks | `superpowers:executing-plans` |
| Plan ready, independent tasks, no complex deps | `superpowers:dispatching-parallel-agents` |
| Plan ready, need review between each task | `superpowers:subagent-driven-development` |

## Example

**Project:** Build auth service + API gateway + integration layer

```
Step 1: /brainstorm
  → Design doc: 3 services, JWT auth, rate limiting, integration tests

Step 2: /write-plan
  → Plan: 10 tasks across 3 workstreams, 4 batches, 2 merge points

Step 3: /workstream
  → Phase 1: Create .worktrees/WS-A, .worktrees/WS-B, .worktrees/WS-C
  → Phase 2: Batch 1 (A1,A2,B1) → Batch 2 (A3,B2) → MP1 → Batch 3 (C1) → Batch 4 (A4,B3,C2) → MP2
  → Phase 3: EXECUTION-DASHBOARD.md created
  → Phase 4: 3 subagents in Batch 1, 2 in Batch 2, review at MP1, 1 in Batch 3, 3 in Batch 4, review at MP2
  → Phase 5: CLAUDE.md updated with JWT format, rate limiting patterns

Step 4: finishing-a-development-branch → PR created, worktrees cleaned up
```

**Result:** 10 tasks across 3 workstreams, up to 3 subagents in parallel, zero conflicts, clean integration.

## Contributing

Contributions welcome:

1. **Testing:** Run baseline scenarios, identify gaps
2. **Examples:** Add real-world multi-workstream project examples
3. **Tooling:** Dashboard automation, dependency graph visualization

## License

MIT

## Links

- [Plugin Repository](https://github.com/celestialdust/Batch-Execution-Stream-Skills)
- [Marketplace](https://github.com/celestialdust/batch-execution-stream-marketplace)
- [Superpowers](https://github.com/obra/superpowers)
- [Issues](https://github.com/celestialdust/Batch-Execution-Stream-Skills/issues)
