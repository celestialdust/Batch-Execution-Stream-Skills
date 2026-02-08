# Skill Review: Workstream-Batch Execution vs Blog Workflow

**Reviewer:** Claude Sonnet 4.5
**Date:** 2026-02-08
**Comparing:** SKILL.md against blog post workflow description

---

## ✅ What the Skill Captures Well

### 1. **Workstream Decomposition** ✅
- ✅ WS-<ID> naming convention matches blog
- ✅ Service boundary → git worktree mapping is explicit
- ✅ "More service boundaries → more worktrees → more parallel agents" principle captured
- ✅ Clear examples (WS-A, WS-B, WS-C)

### 2. **Batch Execution Model** ✅
- ✅ Batches and waves defined clearly
- ✅ Parallel vs sequential vs convergence explicitly modeled
- ✅ Example matches blog format: Batch 1 (A1, B1, E1) → Batch 2 → MP1
- ✅ Dependency modeling is thorough

### 3. **Merge Points** ✅
- ✅ MP (Merge Point) notation matches blog
- ✅ "Where parallel work converges" explicitly stated
- ✅ Example: MP1: A8 + B3 → unlocks C1 format matches blog
- ✅ Integration testing at merge points required

### 4. **Dashboard Tracking** ✅
- ✅ ASCII dashboard format
- ✅ Status indicators: ✅ Done, 🔄 Active, ⏸️ Blocked, ⏳ Runnable
- ✅ Shows which batches done vs active vs blocked
- ✅ Critical path visualization included
- ✅ Example state: "Batches 1-7 complete, Batch 8 active, Batch 9 blocked" format captured

### 5. **Learning Feedback Loop** ✅
- ✅ Capture learnings to CLAUDE.md emphasized
- ✅ "Where specs wrong, assumptions break" language matches blog
- ✅ Feedback loop fixes issues early - principle stated
- ✅ Example learnings section included

---

## ⚠️ Gaps or Weaknesses

### 1. **Task-Level Tracking Metadata** ⚠️
**Blog says:** "Every task has spec, tracking metadata, and verification"
**Skill says:** Mentions verification but doesn't specify what "tracking metadata" means

**Gap:** No explicit guidance on:
- What metadata to track per task (started time, assigned agent, status)
- How to document task spec vs acceptance criteria
- Format for tracking metadata (in dashboard, separate file, or git commit messages)

### 2. **Cross-Session Execution** ⚠️
**Blog says:** "Tasks run across multiple Cursor + Claude sessions — parallel where they can be, sequential where dependencies force it"
**Skill says:** Uses superpowers:dispatching-parallel-agents and subagent-driven-development but doesn't explicitly address multi-session coordination

**Gap:**
- No guidance on how agents coordinate across multiple Claude Code sessions
- No mention of session handoffs or state preservation between sessions
- Assumes all execution happens in one long session

### 3. **"Waves" Terminology** ⚠️
**Blog says:** Groups tasks into "batches and waves"
**Skill says:** Defines "Wave" in Quick Reference but never uses it in examples or process

**Gap:**
- Wave vs Batch distinction unclear
- No examples showing wave progression
- Quick Reference defines it but execution phases don't reference it

### 4. **Verification Before Unblocking** ⚠️
**Blog says:** "Verification before anything downstream unblocks"
**Skill says:** "Always verify tests pass before marking task complete" but not as strongly emphasized

**Gap:**
- Could be more explicit about blocking downstream work until verification passes
- No specific examples of what happens if verification fails (rollback? block entire batch?)

### 5. **Dashboard Update Frequency** ⚠️
**Blog emphasis:** Real-time state visibility is critical
**Skill says:** "Update after each task/batch completion"

**Gap:**
- Doesn't emphasize updating dashboard at START of task (mark as 🔄 Active)
- No guidance on updating when task hits blocker mid-execution
- Missing "Active Work" tracking (which agent on which task right now)

---

## 💡 Specific Improvement Suggestions

### 1. **Add Task-Level Tracking Section**

Add to Phase 2 or Phase 4:

```markdown
### Task Tracking Metadata

Every task must document:
- **Task ID:** WS-Letter + Number (e.g., A1, B3)
- **Spec:** What needs to be done (from implementation plan)
- **Acceptance Criteria:** How to verify it's done correctly
- **Status:** ⏳ Runnable → 🔄 Active → ✅ Done
- **Assigned Agent:** Which agent/session is working on it
- **Started:** Timestamp when work began
- **Completed:** Timestamp when verification passed
- **Blockers:** Any issues preventing progress

**Document in:** Dashboard "Active Work" section or git commit messages
```

### 2. **Add Multi-Session Coordination Guidance**

Add to Phase 4:

```markdown
### Cross-Session Execution

**For parallel work across multiple Claude Code sessions:**

1. **Session Assignment:**
   - Each workstream can run in separate Claude Code session
   - WS-A in Session 1, WS-B in Session 2, WS-C in Session 3
   - Sessions work independently until merge point

2. **State Coordination:**
   - Dashboard file is shared across sessions (git)
   - Each session updates dashboard when task starts/completes
   - Pull dashboard updates before starting new task

3. **Merge Point Coordination:**
   - All sessions must reach merge point before proceeding
   - One session designated as "integration lead" for merge point
   - Other sessions pause until merge point cleared

**Example:**
```
Session 1 (WS-A): A1 ✅ → A2 ✅ → A3 ✅ → Wait at MP1
Session 2 (WS-B): B1 ✅ → B2 ✅ → Wait at MP1
Session 3 (Integration): Pulls A3 + B2 → Integration test → MP1 ✅ → Unblocks C1
```
```

### 3. **Clarify Waves vs Batches**

Update Quick Reference:

```markdown
| **Batch** | Group of tasks at same dependency level (can run in parallel) | Batch 1: A1, B1, E1 |
| **Wave** | Sequential sequence of multiple batches | Wave 1: [Batch 1 → Batch 2] → Wave 2: [Batch 3 → Batch 4] |
```

Add example showing waves:

```markdown
**Wave Structure Example:**

Wave 1: Foundation
  ├─ Batch 1: A1, B1, E1 (parallel)
  └─ Batch 2: A2, B2 (parallel, blocked on Batch 1)

Merge Point MP1: Integration test

Wave 2: Integration
  ├─ Batch 3: C1, C2 (parallel, blocked on MP1)
  └─ Batch 4: A3, B3, C3 (parallel, blocked on Batch 3)

Merge Point MP2: Final integration
```

### 4. **Strengthen Verification-Before-Unblock**

Update Critical Rules in Phase 4:

```markdown
**Critical rules:**
- ❌ Never start batch N+1 until batch N is 100% complete AND verified
- ❌ Never skip merge points (integration issues compound)
- ❌ Never dispatch parallel agents that touch same files
- ❌ **BLOCKING RULE:** Task marked ✅ ONLY after verification passes (tests green, review approved)
- ❌ **If verification fails:** Task stays 🔄 Active, downstream stays ⏸️ Blocked
- ✅ Always verify tests pass before marking task complete
- ✅ Always run integration tests at merge points before unblocking downstream
```

### 5. **Add "Active Work" to Dashboard Template**

Update dashboard template to include:

```markdown
## Active Work

| Agent/Session | Workstream | Task | Started | Status |
|---------------|-----------|------|---------|---------|
| Session-1 | WS-A | A3 | 2026-02-08 14:23 | 🔄 In progress |
| Session-2 | WS-B | B2 | 2026-02-08 14:25 | 🔄 In progress |
| Session-3 | WS-C | - | - | ⏸️ Waiting on MP1 |

**Update this table:**
- When starting a task (mark 🔄)
- When completing a task (remove from table, update batch progress)
- When hitting a blocker (add blocker note)
```

### 6. **Add "What If" Troubleshooting Section**

Add new section after Common Mistakes:

```markdown
## Troubleshooting: When Things Go Wrong

### Task Verification Fails
- **Don't:** Mark task complete anyway
- **Do:** Keep task 🔄 Active, fix issue, re-verify, then mark ✅
- **Impact:** Downstream tasks stay ⏸️ Blocked until fixed

### Merge Point Integration Test Fails
- **Don't:** Skip the merge point or merge anyway
- **Do:** Identify which workstream has issue, fix in that worktree, re-test
- **Impact:** All downstream batches blocked until MP cleared

### Hidden Dependency Discovered Mid-Batch
- **Don't:** Continue parallel execution
- **Do:** Pause affected tasks, update dependency model, re-sequence batches
- **Update:** Dashboard + implementation plan with corrected dependencies

### Agent Conflict (Two Agents Touch Same Files)
- **Don't:** Try to merge conflicting changes
- **Do:** STOP - this means workstream decomposition was wrong
- **Fix:** Re-decompose workstreams with clearer boundaries, restart affected tasks
```

---

## 🎯 Priority Recommendations

### Priority 1: Add Task-Level Tracking Metadata (HIGH IMPACT)
**Why:** Blog explicitly mentions "every task has spec, tracking metadata, and verification"
**Current gap:** Skill says what to verify but not what to track
**Fix:** Add "Task Tracking Metadata" section to Phase 2 or 4
**Effort:** 30 minutes

### Priority 2: Add Multi-Session Coordination (HIGH IMPACT)
**Why:** Blog mentions "tasks run across multiple Cursor + Claude sessions"
**Current gap:** Skill assumes single-session execution
**Fix:** Add "Cross-Session Execution" guidance to Phase 4
**Effort:** 45 minutes
**Benefit:** Makes skill actually usable for 5-10 parallel agents (the stated goal)

### Priority 3: Strengthen Verification-Before-Unblock (MEDIUM IMPACT)
**Why:** Blog emphasizes "verification before anything downstream unblocks"
**Current gap:** Mentioned but not strongly enforced
**Fix:** Update Critical Rules with explicit blocking behavior
**Effort:** 15 minutes
**Benefit:** Prevents "optimistic execution" where downstream starts before upstream verified

---

## Overall Assessment

**Score: 8.5/10** - Skill captures the core workflow very well but has gaps in operational details

**Strengths:**
- Workstream decomposition is excellent
- Batch/wave modeling is clear and matches blog
- Dashboard template is comprehensive
- Learning feedback loop is well-explained
- Examples are concrete and helpful

**Weaknesses:**
- Missing task-level tracking metadata details
- Doesn't address multi-session coordination (critical for 5-10 agents)
- Wave terminology defined but not used
- Verification-before-unblock could be stronger
- Active work tracking missing from dashboard

**Recommendation:** Implement Priority 1 and 2 suggestions to bring score to 9.5/10 and make the skill production-ready for actual 8-agent parallel execution.
