---
name: workstream-batch-execution
description: Use when executing approved design docs with multiple service boundaries, complex task dependencies, and need to coordinate parallel agent work across workstreams
---

# Workstream-Batch Execution

## Overview

Execute large implementation plans by mapping service boundaries to isolated workstreams, modeling task dependencies into batches and waves, and orchestrating parallel agent work with merge points.

**Core principle:** Explicit specification of what can run in parallel, what must be sequential, and where parallel work converges = safe, scalable parallelism across 5-10+ agents.

**Announce at start:** "I'm using the workstream-batch-execution skill to orchestrate this implementation."

## When to Use

```dot
digraph when_to_use {
    "Approved design doc?" [shape=diamond];
    "Multiple service boundaries?" [shape=diamond];
    "Complex task dependencies?" [shape=diamond];
    "Sequential execution\n(executing-plans or\nsubagent-driven-development)" [shape=box];
    "workstream-batch-execution" [shape=box style=filled fillcolor=lightgreen];

    "Approved design doc?" -> "Multiple service boundaries?" [label="yes"];
    "Approved design doc?" -> "Sequential execution\n(executing-plans or\nsubagent-driven-development)" [label="no"];
    "Multiple service boundaries?" -> "Complex task dependencies?" [label="yes (2+)"];
    "Multiple service boundaries?" -> "Sequential execution\n(executing-plans or\nsubagent-driven-development)" [label="no (1)"];
    "Complex task dependencies?" -> "workstream-batch-execution" [label="yes"];
    "Complex task dependencies?" -> "Sequential execution\n(executing-plans or\nsubagent-driven-development)" [label="no"];
}
```

**Use when:**
- Design doc approved with multiple service boundaries (2+ independent services/modules)
- Tasks have complex dependencies (parallel work, sequential work, convergence points)
- Need to coordinate 5-10+ parallel agents without conflicts
- Large implementation requiring orchestration (not just sequential execution)

**Don't use when:**
- Single service/module (use superpowers:subagent-driven-development)
- Simple linear dependencies (use superpowers:executing-plans)
- Design not approved yet (use superpowers:brainstorming first)
- All tasks tightly coupled (no parallelization possible)

## Quick Reference

| Element | Definition | Example |
|---------|-----------|---------|
| **Workstream (WS-<ID>)** | Independent service boundary | WS-1: Auth Service, WS-2: API Gateway |
| **Batch** | Group of tasks with same dependency level | Batch 1: A1, B1, E1 (all parallel) |
| **Wave** | Sequential grouping of batches | Wave 1 → Wave 2 → Wave 3 |
| **Merge Point (MP)** | Convergence where parallel work integrates | MP1: A8 + B3 → unlocks C1 |
| **Task ID** | Workstream + sequence number | A1 = first task in WS-A |

## The Five-Phase Process

### Phase 1: Workstream Decomposition

**Goal:** Map service boundaries to isolated workstreams.

**Process:**

1. **Identify service boundaries** from design doc
   - Each service/module/component that can be developed independently
   - Clear interfaces/contracts between boundaries
   - Minimal shared state

2. **Assign workstream IDs** (WS-A, WS-B, WS-C...)
   - Use letters for easy reference
   - Document what each workstream owns

3. **Create worktrees** for each workstream
   - **REQUIRED SUB-SKILL:** Use superpowers:using-git-worktrees for each WS
   - Pattern: `.worktrees/WS-A`, `.worktrees/WS-B`, etc.
   - Each worktree = isolated workspace for that service boundary

**Example:**

```markdown
## Workstream Decomposition

**WS-A: Authentication Service**
- Handles user auth, sessions, tokens
- Files: `services/auth/*`, `tests/auth/*`
- Worktree: `.worktrees/WS-A`

**WS-B: API Gateway**
- Routes requests, rate limiting, validation
- Files: `services/gateway/*`, `tests/gateway/*`
- Worktree: `.worktrees/WS-B`

**WS-C: Integration Layer**
- Connects A+B, end-to-end flows
- Files: `services/integration/*`, `tests/integration/*`
- Worktree: `.worktrees/WS-C`
```

### Phase 2: Task Extraction and Dependency Modeling

**Goal:** Break workstreams into tasks and model dependencies.

**Process:**

1. **Extract tasks from design doc/plan**
   - Each task is actionable unit (2-5 minutes per step as per writing-plans)
   - Assign task IDs: `<WS-Letter><Number>` (A1, A2, B1, B2...)
   - Document task clearly with acceptance criteria

2. **Model dependencies**
   - **Parallel:** Tasks with no dependencies (can run simultaneously)
   - **Sequential:** Task B blocked by Task A (A must complete first)
   - **Convergence:** Multiple tasks must complete before next unlocks

3. **Create batch groups**
   - Batch = all tasks at same dependency level
   - Tasks in same batch CAN run in parallel
   - Batches run sequentially (Batch 2 waits for Batch 1)

4. **Identify merge points**
   - Points where parallel work must integrate
   - Code review + integration testing required
   - Blocks downstream work until merged

**Example:**

```markdown
## Task Dependencies

### Batch 1 (Parallel - no dependencies)
- **A1:** Auth service data models
- **A2:** Auth API endpoints
- **B1:** Gateway routing logic
- **E1:** Shared types/interfaces

### Batch 2 (Blocked on Batch 1)
- **A3:** Auth middleware (needs A1, A2)
- **B2:** Rate limiting (needs B1)

### Merge Point MP1
- **Trigger:** A3 + B2 complete
- **Action:** Integration test auth → gateway flow
- **Unlocks:** C1

### Batch 3 (Blocked on MP1)
- **C1:** End-to-end auth flow (needs MP1)
```

### Phase 3: Dashboard Setup

**Goal:** Create visual tracking for execution state.

**Process:**

1. **Create dashboard file** in project root or docs/
   - `EXECUTION-DASHBOARD.md` or similar
   - ASCII format for easy updates

2. **Initialize with structure** (see template below)
   - Batch status (✅ Done, 🔄 Active, ⏸️ Blocked, ⏳ Runnable)
   - Task status per workstream
   - Merge point tracking
   - Critical path visualization

3. **Update after each task/batch** completion
   - Keep state visible and accurate
   - Helps identify bottlenecks

**Template:** See dashboard-template.md in this directory.

### Phase 4: Batch Execution

**Goal:** Execute tasks with optimal parallelization.

**Process:**

For each batch (in order):

1. **Identify runnable tasks** in current batch
   - All dependencies met?
   - Merge points cleared?

2. **Decide execution strategy:**

   **If all tasks in batch are independent:**
   - **REQUIRED SUB-SKILL:** Use superpowers:dispatching-parallel-agents
   - Dispatch one agent per task in parallel
   - All agents work simultaneously in their respective worktrees

   **If tasks have subtle dependencies or need review between:**
   - **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
   - Execute tasks sequentially with review checkpoints

3. **For each task:**
   - Agent works in assigned worktree
   - Follows TDD (superpowers:test-driven-development)
   - Runs verification before marking complete
   - Commits to worktree's branch

4. **Update dashboard** after each task completion
   - Mark task ✅
   - Update batch status
   - Check if merge point triggered

5. **At merge points:**
   - **REQUIRED SUB-SKILL:** Use superpowers:requesting-code-review
   - Review integrated changes from multiple workstreams
   - Run integration tests
   - Resolve conflicts if any
   - Mark merge point complete

6. **Verify batch complete:**
   - All tasks in batch ✅?
   - All merge points cleared?
   - Run cross-workstream tests if applicable

7. **Proceed to next batch**

**Critical rules:**
- Never start batch N+1 until batch N is 100% complete
- Never skip merge points (integration issues compound)
- Never dispatch parallel agents that touch same files
- Always verify tests pass before marking task complete

### Phase 5: Learning Capture

**Goal:** Feed insights back into project documentation.

**Process:**

1. **After each batch or at merge points, capture:**
   - What went wrong (specs wrong, assumptions broken, unclear requirements)
   - What worked well (patterns to repeat)
   - What needs tightening (ambiguous instructions, missing context)

2. **Update CLAUDE.md** with learnings
   - Common mistakes section
   - Clarifications for future agents
   - Project-specific patterns that emerged

3. **Update execution plan if needed**
   - Adjust remaining batches based on learnings
   - Revise task dependencies if assumptions changed

**Example:**

```markdown
## Captured in CLAUDE.md after Batch 1:

### Common Mistakes
- Auth tokens: Use JWT format (not sessions) - emerged from A2 implementation
- Rate limiting: Apply per-endpoint not globally - B1 initially wrong

### Patterns
- Always validate interfaces at workstream boundaries - caught issues at MP1
```

## Dashboard Template

Create `EXECUTION-DASHBOARD.md` in your project:

```markdown
# Execution Dashboard

**Updated:** [timestamp]

## Workstream Status

| WS | Name | Branch | Status | Tasks Complete |
|----|------|--------|--------|----------------|
| A | Auth Service | `.worktrees/WS-A` | 🔄 Active | 3/8 |
| B | API Gateway | `.worktrees/WS-B` | ✅ Done | 3/3 |
| C | Integration | `.worktrees/WS-C` | ⏸️ Blocked (MP1) | 0/2 |

## Batch Progress

```
Legend: ✅ Done  🔄 Active  ⏸️ Blocked  ⏳ Runnable

Batch 1: ✅ DONE
  A1 ✅  Auth models
  A2 ✅  Auth endpoints
  B1 ✅  Gateway routing
  E1 ✅  Shared types

Batch 2: ✅ DONE
  A3 ✅  Auth middleware
  B2 ✅  Rate limiting

Merge Point MP1: ✅ DONE
  ✅ Integration test: auth → gateway
  ✅ Code review passed
  → Unlocked: C1

Batch 3: 🔄 ACTIVE
  C1 🔄  End-to-end flow (in progress)

Batch 4: ⏸️ BLOCKED (waiting on Batch 3)
  A4 ⏸️  Token refresh
  B3 ⏸️  Error handling

Merge Point MP2: ⏸️ BLOCKED (waiting on A4 + B3)
  ⏸️ Full integration test
  → Will unlock: Batch 5
```

## Critical Path

```
A1 → A2 → A3 → [MP1] → C1 → [Batch 4] → A4 → [MP2] → Final Integration
```

Current bottleneck: **C1** (end-to-end flow implementation)
```

## Integration with Existing Skills

This skill orchestrates other superpowers skills:

| Phase | Calls Skill |
|-------|-------------|
| **Workstream Decomposition** | superpowers:using-git-worktrees (per workstream) |
| **Parallel Batches** | superpowers:dispatching-parallel-agents |
| **Sequential Batches** | superpowers:subagent-driven-development |
| **Merge Points** | superpowers:requesting-code-review |
| **Final Integration** | superpowers:finishing-a-development-branch |
| **Each Task** | superpowers:test-driven-development (via subagents) |
| **Before Starting** | superpowers:brainstorming (design/planning) |
| **Verification** | superpowers:verification-before-completion |

## Common Mistakes

### ❌ Starting parallel work without workstream isolation
- **Problem:** Agents conflict on same files, merge chaos
- **Fix:** Always create separate worktrees per workstream first

### ❌ Skipping merge points
- **Problem:** Integration issues compound, final merge is nightmare
- **Fix:** Review and test at EVERY merge point, never skip

### ❌ Dispatching dependent tasks in parallel
- **Problem:** Task B starts before Task A completes, fails or duplicates work
- **Fix:** Model dependencies correctly, batch only truly independent tasks

### ❌ No dashboard = lost in complexity
- **Problem:** Can't tell what's blocked, what's runnable, where bottlenecks are
- **Fix:** Update dashboard after every task, make state visible

### ❌ Ignoring learning feedback
- **Problem:** Same mistakes repeat across workstreams
- **Fix:** Capture learnings to CLAUDE.md immediately, propagate to other agents

### ❌ Optimistic dependency modeling
- **Problem:** "These seem independent" → discover hidden dependency mid-execution
- **Fix:** Conservative batching (when unsure, sequential first batch, parallelize later batches)

## Red Flags - STOP and Reconsider

**Never:**
- Start parallel agents without workstream isolation (use worktrees)
- Skip merge point reviews ("we'll test at the end")
- Proceed to next batch while current batch has incomplete tasks
- Let agents work on overlapping files/code
- Ignore failing tests at batch boundaries
- Skip dashboard updates (state becomes invisible)

**Always:**
- Create worktrees before dispatching agents
- Model dependencies conservatively
- Review at merge points before proceeding
- Update dashboard to maintain visibility
- Capture learnings back to CLAUDE.md
- Verify tests pass before batch completion

## Real-World Impact

From blog post (8 agents in parallel):
- **Before:** Sequential execution, agents waiting on each other
- **After:** Workstream isolation + batch modeling + dashboard tracking
- **Result:** 8 agents working simultaneously without conflicts
- **Key insight:** "Specs surface where assumptions break" - feedback loop critical

## Example: Three-Service Implementation

**Design doc approved:** Build auth service, API gateway, integration layer

**Phase 1: Workstream Decomposition**
```
WS-A: Auth Service → .worktrees/WS-A
WS-B: API Gateway → .worktrees/WS-B
WS-C: Integration → .worktrees/WS-C
```

**Phase 2: Dependency Modeling**
```
Batch 1 (Parallel): A1, A2, B1 - foundation work, all independent
Batch 2 (Parallel): A3, B2 - build on Batch 1, no cross-dependencies
MP1: Integrate A+B, test auth→gateway flow
Batch 3 (Sequential): C1 - needs MP1 complete
```

**Phase 3: Dashboard Setup**
```markdown
EXECUTION-DASHBOARD.md created with 3 workstreams, 3 batches, 1 merge point
```

**Phase 4: Execution**
1. Batch 1: Dispatch 3 parallel agents (A1, A2, B1) via dispatching-parallel-agents
2. All complete → Update dashboard → Batch 1 ✅
3. Batch 2: Dispatch 2 parallel agents (A3, B2)
4. All complete → Update dashboard → Batch 2 ✅
5. MP1: Code review, integration test → MP1 ✅
6. Batch 3: Single agent (C1) via subagent-driven-development
7. Complete → Update dashboard → Batch 3 ✅

**Phase 5: Learning Capture**
```markdown
CLAUDE.md updated:
- Auth tokens must be JWT (discovered in A2)
- Rate limiting per-endpoint not global (B1 initially wrong)
- Integration tests caught interface mismatch at MP1
```

**Result:** 3 workstreams completed with 5 agents working in parallel (3 in Batch 1, 2 in Batch 2, no conflicts, clean integration).
