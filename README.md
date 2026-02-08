# Workstream-Batch Execution Skill

A comprehensive skill for Claude Code/AI agents to execute large implementation plans by orchestrating parallel agent work across multiple service boundaries.

## Overview

Execute multi-workstream projects by:
- **Workstream Decomposition:** Map service boundaries to isolated git worktrees
- **Dependency Modeling:** Organize tasks into batches, waves, and merge points
- **Smart Parallelization:** Dispatch 5-10+ agents simultaneously without conflicts
- **Visual Tracking:** ASCII dashboard for execution state visibility
- **Learning Feedback:** Systematic capture of insights back to project docs

## Inspired By

This skill captures the workflow from [this blog post](https://x.com/xuejoey) on running 8 AI agents in parallel off a single design doc through explicit specification and execution modeling.

## Files

- **SKILL.md** - Main skill documentation with 5-phase process
- **dashboard-template.md** - Ready-to-use ASCII dashboard template
- **integration-guide.md** - Reference for orchestrating existing superpowers skills

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

### For Claude Code

Copy to your Claude Code skills directory:

```bash
cp -r . ~/.claude/skills/workstream-batch-execution/
```

### For Other AI Agents

Adapt the skill to your agent's skill system. The core concepts (workstream decomposition, batch modeling, dashboard tracking) are tool-agnostic.

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
