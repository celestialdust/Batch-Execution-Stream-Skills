# Execution Dashboard Template

Copy this template to your project root or docs/ folder as `EXECUTION-DASHBOARD.md`.

Update after each task completion to maintain visibility.

---

```markdown
# Execution Dashboard

**Project:** [Project Name]
**Started:** [YYYY-MM-DD]
**Updated:** [YYYY-MM-DD HH:MM]

## Workstream Status

| WS | Name | Branch | Status | Tasks Complete |
|----|------|--------|--------|----------------|
| A | [Service/Module Name] | `.worktrees/WS-A` | ⏳ Runnable | 0/5 |
| B | [Service/Module Name] | `.worktrees/WS-B` | ⏳ Runnable | 0/3 |
| C | [Service/Module Name] | `.worktrees/WS-C` | ⏸️ Blocked | 0/2 |

**Legend:**
- ✅ Done - All tasks complete
- 🔄 Active - Currently working
- ⏳ Runnable - Dependencies met, ready to start
- ⏸️ Blocked - Waiting on dependencies/merge points

## Batch Progress

```
Legend: ✅ Done  🔄 Active  ⏸️ Blocked  ⏳ Runnable

Batch 1: ⏳ RUNNABLE (no dependencies)
  A1 ⏳  [Task description]
  A2 ⏳  [Task description]
  B1 ⏳  [Task description]

Batch 2: ⏸️ BLOCKED (waiting on Batch 1)
  A3 ⏸️  [Task description]
  B2 ⏸️  [Task description]

Merge Point MP1: ⏸️ BLOCKED (waiting on A3 + B2)
  ⏸️ [Integration test description]
  ⏸️ Code review
  → Will unlock: C1

Batch 3: ⏸️ BLOCKED (waiting on MP1)
  C1 ⏸️  [Task description]

Batch 4: ⏸️ BLOCKED (waiting on Batch 3)
  A4 ⏸️  [Task description]
  B3 ⏸️  [Task description]

Merge Point MP2: ⏸️ BLOCKED (waiting on A4 + B3)
  ⏸️ [Integration test description]
  → Will unlock: Batch 5

Batch 5: ⏸️ BLOCKED (waiting on MP2)
  A5 ⏸️  [Task description]
  C2 ⏸️  [Task description]
```

## Critical Path

Identify the longest dependency chain from start to finish:

```
[Task] → [Task] → [MP] → [Task] → [MP] → [Task] → Complete

Example:
A1 → A2 → A3 → [MP1] → C1 → A4 → [MP2] → A5 → Final Integration
```

**Current bottleneck:** [Identify current blocking task]

**Estimated completion:** [Based on critical path progress]

## Active Work (Single Session - Parallel Subagents)

All subagents dispatched from this controller session via Task tool.

| Subagent | Worktree | Task | Dispatched | Status | Verification |
|----------|----------|------|------------|--------|-------------|
| Subagent-1 | `.worktrees/WS-A` | A1 | [timestamp] | 🔄 Running | Pending |
| Subagent-2 | `.worktrees/WS-B` | B1 | [timestamp] | 🔄 Running | Pending |
| Subagent-3 | `.worktrees/WS-A` | A2 | [timestamp] | ✅ Returned | ✅ Tests pass |

**Update this table when:**
- Dispatching a subagent (add row, mark 🔄 Running)
- Subagent returns (mark ✅ Returned, record verification result)
- Verification fails (mark 🚫 Failed, add to Issues & Blockers)
- Promoting to ✅ Done in batch progress (remove from active work)

## Completed Work Log

| Date | Task | Workstream | Duration | Notes |
|------|------|-----------|----------|-------|
| [YYYY-MM-DD] | A1 | WS-A | 15 min | Auth models implemented |
| [YYYY-MM-DD] | B1 | WS-B | 12 min | Gateway routing done |

## Issues & Blockers

| Discovered | Task | Issue | Resolution | Status |
|-----------|------|-------|------------|--------|
| [YYYY-MM-DD] | A3 | JWT format unclear | Added to CLAUDE.md | ✅ Resolved |
| [YYYY-MM-DD] | MP1 | Integration test failing | Debugging | 🔄 In progress |

## Learnings Captured

Track learnings fed back to CLAUDE.md:

- **[YYYY-MM-DD]:** Auth tokens must use JWT format (from A2)
- **[YYYY-MM-DD]:** Rate limiting should be per-endpoint (from B1)
- **[YYYY-MM-DD]:** Always validate interfaces at boundaries (from MP1)

## Statistics

- **Total Tasks:** [N]
- **Completed:** [N] ([%])
- **In Progress:** [N]
- **Blocked:** [N]
- **Total Batches:** [N]
- **Batches Complete:** [N]
- **Merge Points:** [N] complete / [N] total
- **Active Subagents:** [N]
- **Max Parallel Subagents:** [N] (in Batch X)

## Next Actions

1. [ ] Complete Batch 1 tasks (A1, A2, B1)
2. [ ] Review at MP1
3. [ ] Begin Batch 2 execution
```

---

## Usage Notes

**Update frequency:**
- When dispatching subagents (mark tasks 🔄 Active)
- When subagents return (verify + mark ✅ Done)
- When starting new batch
- At merge points
- When blockers discovered

**Keep visible:**
- Pin in project root or docs/
- Reference in standup/sync
- Link from CLAUDE.md

**Dashboard benefits:**
- See execution state at a glance
- Identify bottlenecks quickly
- Track critical path progress
- Prevent subagents from stepping on each other
- Track subagent dispatch and verification status
