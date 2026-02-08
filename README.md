# Batch Execution Stream Plugin

A Claude Code plugin for orchestrating parallel agent work across multiple service boundaries with workstream-batch execution model.

## Overview

Execute multi-workstream projects by:
- **Workstream Decomposition:** Map service boundaries to isolated git worktrees
- **Dependency Modeling:** Organize tasks into batches, waves, and merge points
- **Smart Parallelization:** Dispatch 5-10+ agents simultaneously without conflicts
- **Visual Tracking:** ASCII dashboard for execution state visibility
- **Learning Feedback:** Systematic capture of insights back to project docs

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

### Method 1: Via Git (Recommended)

Install directly from GitHub:

```bash
# Clone to Claude Code plugins directory
git clone https://github.com/celestialdust/Batch-Execution-Stream-Skills.git ~/.claude/plugins/batch-execution-stream

# Or for project-specific installation
git clone https://github.com/celestialdust/Batch-Execution-Stream-Skills.git .claude/plugins/batch-execution-stream
```

### Method 2: Manual Installation

1. Download or clone this repository
2. Copy to one of these locations:
   - **User-level:** `~/.claude/plugins/batch-execution-stream/`
   - **Project-level:** `.claude/plugins/batch-execution-stream/`

### Method 3: Via Claude Code Plugin Manager (Future)

Once published to the official marketplace:

```
/plugin install batch-execution-stream
```

### Verify Installation

Start Claude Code and verify the plugin is loaded:

```
/plugin list
```

You should see `batch-execution-stream` in the list.

## Usage

### Option 1: Slash Command (Quick)

Use the custom command to invoke the skill:

```
/workstream
```

This immediately activates the workstream-batch-execution skill.

### Option 2: Direct Skill Invocation

Reference the skill directly in your conversation:

```
Use batch-execution-stream:workstream-batch-execution to orchestrate this implementation
```

### Option 3: Let Claude Discover

When you have a multi-workstream project, Claude will automatically discover and suggest this skill based on your project structure.

## Example Use Case

**Project:** Build auth service, API gateway, and integration layer

**Workstreams:**
- WS-A: Auth Service → `.worktrees/WS-A`
- WS-B: API Gateway → `.worktrees/WS-B`
- WS-C: Integration → `.worktrees/WS-C`

**Execution:**
```
Batch 1 (Parallel): A1, A2, B1 → 3 agents simultaneously
Batch 2 (Parallel): A3, B2 → 2 agents simultaneously
Merge Point MP1: Integration test auth→gateway flow
Batch 3 (Sequential): C1 → 1 agent with reviews
```

**Result:** 3 workstreams completed with 5 agents in parallel, no conflicts, clean integration.

## Contributing

This skill is under active development. Contributions welcome:

1. **Testing:** Run baseline scenarios, identify rationalizations agents use
2. **Examples:** Add real-world examples of multi-workstream projects
3. **Tooling:** Build dashboard automation, dependency graph visualization
4. **Integration:** Connect with other execution frameworks

## TDD Status

Following Test-Driven Development for skills:

- ✅ **GREEN Phase:** Initial skill written
- ⏳ **RED Phase:** Baseline testing needed
- ⏳ **REFACTOR Phase:** Bulletproofing against rationalizations

See `writing-skills` discipline for TDD methodology applied to documentation.

## License

MIT - Use freely, attribute if sharing publicly.

## Contact

For questions, issues, or collaboration: [GitHub Issues](https://github.com/celestialdust/Batch-Execution-Stream-Skills/issues)

---

**Status:** Initial release - feedback and iteration welcome!
