---
name: 'step-04-complete'
description: 'Finalize the run - display summary of successes and failures, close error log'
---

# Step 4: Completion

## STEP GOAL:

Finalize the autonomous build cycle run by writing the final summary to the error log and displaying a clear report of what succeeded and what failed.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are an autonomous build cycle orchestrator completing your run
- ✅ You report clearly and concisely - successes first, then failures
- ✅ This is the only step where you display results to the user

### Step-Specific Rules:

- 🎯 Read the error log and build a clear summary
- 💾 Update the error log file with a final summary section
- 📋 Display results grouped by epic and story

## EXECUTION PROTOCOLS:

- 🎯 Load error_log_path and parse all logged entries
- 💾 Append final summary section to error log file
- 📋 Display summary to user

## CONTEXT BOUNDARIES:

- Available: epic_numbers, stories_per_epic, error_log_path from step-01
- Focus: Summarize and report only - no more execution
- Dependencies: step-02 and step-03 must have completed

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Parse Error Log

Load the error log file at `error_log_path` and parse all `[FAILED]` and `[OK]` entries to build a complete picture of the run.

### 2. Write Final Summary to Error Log

Append a `## Summary` section to the error log file:

```markdown
## Summary

**Run completed:** {current_datetime}
**Epics processed:** {epic_numbers}

### Results by Epic

{for each epic}
#### Epic {n}
| Step | Story | Status |
|------|-------|--------|
| create-story | Story {n} | ✅ OK / ❌ FAILED / ⏭️ SKIPPED (existed) |
| dev-story | Story {n} | ✅ OK / ❌ FAILED |
| code-review | Story {n} | ✅ OK / ❌ FAILED |
| automate | - | ✅ OK / ❌ FAILED |
| retrospective | - | ✅ OK / ❌ FAILED |

### Totals
- Stories fully completed: {count}
- Stories partially completed: {count}
- Stories with failures: {count}
- Epic wrap-ups completed: {count}
- Epic wrap-ups with failures: {count}
```

### 3. Display Run Summary to User

Display the full summary in the terminal:

```
═══════════════════════════════════════
  EPIC AUTO RUNNER - COMPLETE
═══════════════════════════════════════

Epics: {epic_numbers}
Completed at: {current_datetime}

{for each epic - brief status line}
Epic {n}: {X}/{total} stories fully complete | automate: ✅/❌ | retro: ✅/❌

{if any failures}
⚠️  {total_failure_count} failure(s) logged to:
    {error_log_path}

{if no failures}
✅ All steps completed successfully!

═══════════════════════════════════════
```

### 4. Done

This is the final step. The workflow is complete. No next step to load.

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Error log updated with final summary
- Clear run report displayed to user
- Successes and failures clearly distinguished
- Error log path visible for user to review

### ❌ SYSTEM FAILURE:

- Not reading the error log before summarizing
- Missing or incomplete summary
- Not displaying results to user

**Master Rule:** This is the final step. There is no next step to load.
