---
name: 'step-03-epic-wrap'
description: 'Run automate (QA) for all involved epics, and catch-up retrospective for any epics fully complete but not yet retrod'

nextStepFile: './step-04-complete.md'
---

# Step 3: Epic Wrap-Up

## STEP GOAL:

Run automate (QA test generation) for every involved epic, then run retrospective catch-up for any epics that are fully complete but whose retro wasn't triggered inline during step-02.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are an autonomous build cycle orchestrator
- ✅ You execute precisely, log failures, and never block
- ✅ A failed epic step is logged and skipped — never a reason to stop
- ✅ You commit after every successful step using Git MCP

### Step-Specific Rules:

- 🎯 Process epics sequentially — one at a time
- 🚫 NEVER wait for human input during this step
- 🚫 NEVER skip an epic wrap-up without logging the reason
- 🎯 Each sub-agent gets a fresh context with only what it needs
- 💾 Use Git MCP for all commits — log error and continue if commit fails
- ⚙️ If sub-agent spawning unavailable: execute each workflow step in main thread sequentially

## EXECUTION PROTOCOLS:

- 🎯 Iterate involved epics in sequential order
- 🎯 Automate runs for ALL involved epics (no conditions)
- 🎯 Retrospective runs ONLY for epics fully complete AND not already retro'd in step-02
- 💾 Commit after each successful step via Git MCP
- 📋 Append all errors to error_log_path with full context
- 🚫 FORBIDDEN to block or halt on any error — log and continue

## CONTEXT BOUNDARIES:

- Available: ordered_stories, all_stories_per_epic, epic_completion_status, error_log_path, run_qa_automate, run_retro_auto from step-01/step-02
- Focus: Epic-level wrap-up only (automate, catch-up retrospective + correct-course)
- Limits: Do NOT re-run story-level steps here. Do NOT re-run retros already done inline. Honour run_qa_automate and run_retro_auto flags.
- Dependencies: step-02 must have completed (even partially)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Derive Involved Epics

From `epic_completion_status`, get the list of all unique epics that had at least one story in the user's ordered list.

### 2. Begin Epic Iteration

For each involved epic (in order):

  Execute the epic wrap-up defined in steps 3-4 below.

### 3. Automate Sub-Agent (QA Test Generation)

**IF `run_qa_automate` is false:**

Log `[SKIPPED] automate epic-{epic_number}: manual run requested by user`
Continue to step 4.

**IF `run_qa_automate` is true — runs for EVERY involved epic:**

Spawn a fresh sub-agent with this context:
```
Agent: QA (bmad-agent-bmm-qa)
Task: Run the automate workflow for Epic {epic_number}
Workflow: /bmad-bmm-automate
Context: epic number, list of stories completed for this epic in this run
Auto-accept: all outputs
Do not ask for confirmation
```

On success: commit via Git MCP with message: `"automate epic-{epic_number}"`
On failure: append to error log:
```
## [FAILED] automate epic-{epic_number}
Error: {error_details}
Action: logged, continuing to retrospective check
```
Continue to step 4 (do not skip retro check on automate failure).

### 4. Retrospective Check and Catch-Up

**Check `epic_completion_status[epic]`:**

- **IF `retro_done` is true:** Log `[INFO] Epic {epic_number}: retrospective already completed inline during step-02. Skipping.` Continue to next epic.
- **IF epic is NOT fully complete:** Log `[INFO] Epic {epic_number}: not all stories complete ({completed}/{total}). Skipping retrospective.` Continue to next epic.
- **IF `run_retro_auto` is false AND retro not done:** Log `[SKIPPED] retrospective epic-{epic_number} (catch-up): manual run requested by user` Continue to next epic.
- **IF epic IS fully complete AND `retro_done` is false AND `run_retro_auto` is true:** This is a catch-up — run retro now.

**Retrospective Sub-Agent (catch-up only):**

Spawn a fresh sub-agent with this context:
```
Agent: SM (bmad-agent-bmm-sm)
Task: Run the retrospective workflow for Epic {epic_number}
Workflow: /bmad-bmm-retrospective
Context: epic number, list of stories and their outcomes (successes and failures from error log)
Auto-accept: all outputs
Do not ask for confirmation
```

On success:
- Set `epic_completion_status[epic].retro_done = true`
- Store `epic_completion_status[epic].retro_doc_path` = path of the retro document produced
- Commit via Git MCP with message: `"retrospective epic-{epic_number} (catch-up)"`
- Proceed immediately to **Correct-Course Sub-Agent** below

On failure: append to error log:
```
## [FAILED] retrospective epic-{epic_number} (catch-up)
Error: {error_details}
Action: logged, continuing to next epic
```
Skip Correct-Course for this epic. Then continue to next epic (do not block).

**Correct-Course Sub-Agent (catch-up — runs only after a successful catch-up retro):**

Log `[CORRECT-COURSE] Epic {epic_number}: applying retrospective fixes (catch-up)...`

Spawn a fresh sub-agent with this context:
```
Agent: Architect (bmad-agent-bmm-architect)
Task: Run the correct-course workflow to implement fixes identified in the Epic {epic_number} retrospective
Workflow: /bmad-bmm-correct-course
Context: epic number, retro document path ({epic_completion_status[epic].retro_doc_path}), list of action items and significant discoveries from the retro
Auto-accept: all outputs, apply all changes automatically
Do not ask for confirmation
```

On success: commit via Git MCP with message: `"correct-course epic-{epic_number} (catch-up)"`
On failure: append to error log:
```
## [FAILED] correct-course epic-{epic_number} (catch-up)
Error: {error_details}
Action: logged, continuing to next epic
```
Then continue to next epic (do not block).

### 5. Epic Complete — Continue Loop

Log `[OK] Epic {epic_number}: wrap-up complete` and proceed to the next epic.

After all involved epics are processed, proceed to step-04.

### 6. Auto-Proceed to Completion

Display: **Epic wrap-up complete for all involved epics. Proceeding to completion summary...**

#### EXECUTION RULES:

- This is an auto-proceed step with no user choices — no halt required
- This is intentional: the workflow is fully autonomous after step-01
- Proceed directly to next step after all epics are wrapped up

#### Menu Handling Logic:

- After all epics wrapped up, immediately load, read entire file, then execute {nextStepFile}

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Automate (QA) ran for every involved epic when run_qa_automate is true
- Automate skipped with log entry when run_qa_automate is false
- Retrospective ran only for fully complete epics not already retro'd inline, when run_retro_auto is true
- Retrospective skipped with log entry when run_retro_auto is false
- Correct-course ran after every successful catch-up retro
- Each successful step has a corresponding commit
- All failures logged to error_log_path with context
- No blocking or waiting for human input
- Proceeds autonomously to step-04

### ❌ SYSTEM FAILURE:

- Blocking on any error instead of logging and continuing
- Waiting for human confirmation at any point
- Running automate when run_qa_automate is false
- Skipping automate without logging when run_qa_automate is true
- Re-running retrospective for an epic already retro'd in step-02
- Running retrospective for an epic not yet fully complete
- Running retrospective when run_retro_auto is false
- Not running correct-course after a successful catch-up retro when run_retro_auto is true
- Processing epics in parallel instead of sequentially

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
