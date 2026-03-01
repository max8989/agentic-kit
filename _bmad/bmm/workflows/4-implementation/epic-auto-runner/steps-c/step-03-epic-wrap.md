---
name: 'step-03-epic-wrap'
description: 'Run epic-level wrap-up for each epic - automate and retrospective, commit after each'

nextStepFile: './step-04-complete.md'
---

# Step 3: Epic Wrap-Up

## STEP GOAL:

For each epic, run the epic-level wrap-up cycle autonomously: automate (QA test generation) → retrospective, committing after each step and logging errors before moving on.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are an autonomous build cycle orchestrator
- ✅ You execute precisely, log failures, and never block
- ✅ A failed epic step is logged and skipped - never a reason to stop
- ✅ You commit after every successful step using Git MCP

### Step-Specific Rules:

- 🎯 Process epics sequentially - one at a time
- 🚫 NEVER wait for human input during this step
- 🚫 NEVER skip an epic wrap-up without logging the reason
- 🎯 Each sub-agent gets a fresh context with only what it needs
- 💾 Use Git MCP for all commits - log error and continue if commit fails
- ⚙️ If sub-agent spawning unavailable: execute each workflow step in main thread sequentially

## EXECUTION PROTOCOLS:

- 🎯 Iterate epics in strict sequential order
- 💾 Commit after each successful step via Git MCP
- 📋 Append all errors to error_log_path with full context
- 🚫 FORBIDDEN to block or halt on any error - log and continue

## CONTEXT BOUNDARIES:

- Available: epic_numbers, stories_per_epic, error_log_path from step-01
- Focus: Epic-level wrap-up only (automate, retrospective)
- Limits: Do NOT re-run story-level steps here
- Dependencies: step-02 must have completed (even partially)

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Begin Epic Iteration

For each epic in `epic_numbers` (in order):

  Execute the epic wrap-up defined in steps 2-3 below.

### 2. Automate Sub-Agent (QA Test Generation)

Spawn a fresh sub-agent with this context:
```
Agent: QA (bmad-agent-bmm-qa)
Task: Run the automate workflow for Epic {epic_number}
Workflow: /bmad-bmm-automate
Context: epic number, list of stories completed in this epic
Auto-accept: all outputs
Do not ask for confirmation
```

On success: commit via Git MCP with message: `"automate epic-{epic_number}"`
On failure: append to error log:
```
## [FAILED] automate epic-{epic_number}
Error: {error_details}
Action: skipping retrospective for this epic, continuing to next epic
```
Then skip to next epic.

### 3. Retrospective Sub-Agent

Spawn a fresh sub-agent with this context:
```
Agent: SM (bmad-agent-bmm-sm)
Task: Run the retrospective workflow for Epic {epic_number}
Workflow: /bmad-bmm-retrospective
Context: epic number, list of stories and their outcomes (successes and failures from error log)
Auto-accept: all outputs
Do not ask for confirmation
```

On success: commit via Git MCP with message: `"retrospective epic-{epic_number}"`
On failure: append to error log:
```
## [FAILED] retrospective epic-{epic_number}
Error: {error_details}
Action: logged, continuing to next epic
```
Then continue to next epic (do not block).

### 4. Epic Complete - Continue Loop

Log `[OK] Epic {epic_number}: wrap-up complete` and proceed to the next epic.

After all epics are processed, proceed to step-04.

### 5. Auto-Proceed to Completion

Display: **Epic wrap-up complete for all epics. Proceeding to completion summary...**

#### EXECUTION RULES:

- This is an auto-proceed step with no user choices - no halt required
- This is intentional: the workflow is fully autonomous after step-01
- Proceed directly to next step after all epics are wrapped up

#### Menu Handling Logic:

- After all epics wrapped up, immediately load, read entire file, then execute {nextStepFile}

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All epics processed through automate and retrospective (success or logged failure)
- Each successful step has a corresponding commit
- All failures logged to error_log_path with context
- No blocking or waiting for human input
- Proceeds autonomously to step-04

### ❌ SYSTEM FAILURE:

- Blocking on any error instead of logging and continuing
- Waiting for human confirmation at any point
- Skipping an epic wrap-up without logging the reason
- Running story-level steps (create-story, dev-story, code-review) in this step
- Processing epics in parallel instead of sequentially

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
