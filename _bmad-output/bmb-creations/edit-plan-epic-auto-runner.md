---
mode: edit
targetWorkflowPath: '_bmad/bmm/workflows/4-implementation/epic-auto-runner'
workflowName: 'epic-auto-runner'
editSessionDate: '2026-03-09'
previousSessionDate: '2026-03-08'
stepsCompleted:
  - step-e-01-assess-workflow.md
  - step-e-02-discover-edits.md
  - step-e-04-direct-edit.md
  - step-e-05-apply-edit.md
  - step-e-07-complete.md
hasValidationReport: true
validationStatus: PASS
---

# Edit Plan: epic-auto-runner

## Workflow Snapshot

**Path:** _bmad/bmm/workflows/4-implementation/epic-auto-runner
**Format:** BMAD Compliant ✅
**Step Folders:** steps-c/ (4 step files)

## Validation Status

Full validation completed 2026-03-08 — PASS on all categories.
Key design observation: Current epic-centric iteration model needs restructuring to support cross-epic story ordering.

---

## Edit Goals

### Direct Changes

**Category:** Multiple (workflow.md + step files + command file)

**Changes Requested:**

#### 1. workflow.md — Update goal & description
- [ ] Change goal from "run build cycle for specified epics" to "run build cycle for a user-specified ordered list of stories across any epics"
- [ ] Update role: still autonomous executor, but with interactive story-order confirmation phase
- [ ] Update architecture principles: explicitly no parallel execution, user confirms order first

#### 2. step-01-init.md — Major rewrite of input model
- [ ] Replace "accept epic numbers" with "accept an ordered list of story IDs" (e.g., 1.1b, 1.8, 2.4, 11.1...)
- [ ] User specifies exact execution order as comma-separated story IDs
- [ ] Resolve each story ID against sprint-status.yaml (validate they exist, get file paths)
- [ ] Derive which epics are involved from the story list (for retro tracking)
- [ ] Load ALL stories per involved epic from sprint-status.yaml (to know when an epic is fully complete)
- [ ] Display run plan as ordered flat list (not grouped by epic)
- [ ] User confirms the order before autonomous execution begins

#### 3. step-02-story-cycle.md — Rewrite iteration model
- [ ] Replace epic→story nested loop with flat sequential iteration over the ordered story list
- [ ] Process stories strictly in user-specified order, one at a time, no parallel
- [ ] After each story completes, check: are ALL stories for that story's epic now complete (across the entire sprint-status.yaml, not just the user's list)?
- [ ] If an epic just became fully complete → immediately run retro for that epic before continuing to next story
- [ ] Track which epics have had retro run (don't run twice)

#### 4. step-03-epic-wrap.md — Modify to handle inline retros
- [ ] Change from "iterate all epics and run retro" to "run retro only for epics that weren't already retro'd inline during step-02"
- [ ] This step becomes a safety net / catch-up for any epics that completed but didn't get retro'd
- [ ] May end up being a no-op if all retros ran inline — that's fine

#### 5. step-04-complete.md — Update summary format
- [ ] Summary should reflect flat ordered list execution (not epic-grouped)
- [ ] Show which epics had retros triggered (and at which point in the sequence)

#### 6. Command file (.claude/commands/bmad-bmm-epic-auto-runner.md)
- [ ] Update description to reflect new workflow behavior

**Rationale:**
In real projects, story dependencies cross epic boundaries. The user needs to do a story from another epic in the middle of developing an epic. The current epic-centric model forces all stories in one epic before moving to the next, which doesn't match how cross-epic dependencies work in practice. The user needs to specify a custom execution order and have stories processed sequentially in that exact order, with epic retros triggered automatically when all stories for an epic are done.

---

## Edits Applied

### Direct Changes Applied

**[workflow.md]** `_bmad/bmm/workflows/4-implementation/epic-auto-runner/workflow.md`
- ✅ Changed: Goal, role, and architecture updated to reflect user-ordered story list model
- User approved: Yes
- Compliance check: Passed

**[step file]** `steps-c/step-01-init.md` (220 lines)
- ✅ Changed: Major rewrite — ordered story list input, epic completion tracking, flat run plan
- User approved: Yes
- Compliance check: Passed

**[step file]** `steps-c/step-02-story-cycle.md` (216 lines)
- ✅ Changed: Major rewrite — flat sequential iteration, inline retro on epic completion
- User approved: Yes
- Compliance check: Passed

**[step file]** `steps-c/step-03-epic-wrap.md` (162 lines)
- ✅ Changed: Automate runs for all involved epics, retro only as catch-up for missed inline retros
- User approved: Yes
- Compliance check: Passed

**[step file]** `steps-c/step-04-complete.md` (140 lines)
- ✅ Changed: Summary now reflects execution-order results + epic-level results with retro trigger info
- User approved: Yes
- Compliance check: Passed

**[command file]** `.claude/commands/bmad-bmm-epic-auto-runner.md`
- ✅ Changed: Description updated to reflect story-order mode
- User approved: Yes
- Compliance check: Passed

**[command file]** `.opencode/commands/bmad-bmm-epic-auto-runner.md`
- ✅ Changed: Description updated to reflect story-order mode
- User approved: Yes
- Compliance check: Passed

**[validation fix]** `workflow.md` frontmatter
- ✅ Fixed: Description field updated from "for specified epics" to match new story-order model
- Caught by: Post-edit validation
- Compliance check: Passed

---

## Completion Summary (Session 1 — 2026-03-08)

**Completed:** 2026-03-08
**Session Duration:** Single session

**Total Edits:** 8
- Validation Fixes: 1
- Direct Changes: 7

**Files Modified:** 7
**Final Validation Status:** PASS with warnings (step-01 220 lines, step-02 216 lines — both within 250 max)

**Workflow is ready for:** Testing — recommend running the workflow end-to-end with a real story list to verify behavior

---

## Session 2 — 2026-03-09

### Edit Goals

#### 1. Global QA automate toggle (step-01, step-03, step-04)
- Ask user at story selection time: run QA automate automatically? (y/n)
- Store as `run_qa_automate` flag
- step-03: skip automate sub-agent with log entry when flag is false
- step-04: show skipped state in summary table

#### 2. Global retro auto/manual toggle (step-01, step-02, step-03, step-04)
- Ask user at story selection time: run retrospective automatically? (y/n)
- Store as `run_retro_auto` flag
- step-02 inline retro: skip with log entry when flag is false
- step-03 catch-up retro: skip with log entry when flag is false
- step-04: show skipped state in summary table

#### 3. Correct-course (fix implementation) after each retro (step-02, step-03, step-04)
- After every successful retro (inline or catch-up), spawn Architect agent with `/bmad-bmm-correct-course`
- Pass retro doc path and epic context
- On success: commit `"correct-course epic-{N}"` (or `"correct-course epic-{N} (catch-up)"`)
- On failure: log and continue
- Only runs when `run_retro_auto` is true (no retro = no fixes)
- step-04: add correct-course column to epic-level results table

### Edits Applied (Session 2)

**[step file]** `steps-c/step-01-init.md`
- ✅ Added §3b: new run mode questions (QA automate + retro) with display prompt
- ✅ Updated CONTEXT BOUNDARIES to include new flags
- ✅ Updated §7 run plan to show run mode summary
- ✅ Updated §8 context store to include new flags
- ✅ Updated SUCCESS/FAILURE metrics

**[step file]** `steps-c/step-02-story-cycle.md`
- ✅ Updated CONTEXT BOUNDARIES to include new flags
- ✅ §6: gated inline retro on `run_retro_auto` flag (skip path + log)
- ✅ §6: added correct-course sub-agent (Architect) after successful inline retro
- ✅ Updated SUCCESS/FAILURE metrics

**[step file]** `steps-c/step-03-epic-wrap.md`
- ✅ Updated CONTEXT BOUNDARIES to include new flags
- ✅ §3: gated automate sub-agent on `run_qa_automate` flag (skip path + log)
- ✅ §4: gated catch-up retro on `run_retro_auto` flag (skip path + log)
- ✅ §4: added correct-course sub-agent (Architect) after successful catch-up retro
- ✅ Updated SUCCESS/FAILURE metrics

**[step file]** `steps-c/step-04-complete.md`
- ✅ Updated CONTEXT BOUNDARIES to include new flags
- ✅ Added correct-course column to epic-level results table
- ✅ Added skipped (manual) states to example rows and totals
- ✅ Updated terminal display to show correct-course status per epic

### Completion Summary (Session 2)

**Completed:** 2026-03-09
**Total Edits:** 4 files modified
**New Features:** run_qa_automate flag, run_retro_auto flag, correct-course post-retro invocation
