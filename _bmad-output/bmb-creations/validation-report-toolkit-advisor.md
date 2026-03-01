---
agentName: 'toolkit-advisor'
hasSidecar: true
module: 'stand-alone'
agentFile: '_bmad/agents/toolkit-advisor/toolkit-advisor.agent.yaml'
validationDate: '2026-03-01'
stepsCompleted:
  - v-01-load-review.md
  - v-02a-validate-metadata.md
  - v-02b-validate-persona.md
  - v-02c-validate-menu.md
  - v-02d-validate-structure.md
  - v-02e-validate-sidecar.md
  - v-03-summary.md
validationStatus: PASS
fixesApplied:
  - communication_style cleaned to pure voice (behavioral phrase removed)
workflowComplete: true
---

# Validation Report: toolkit-advisor

## Agent Overview

**Name:** Forge (toolkit-advisor)
**hasSidecar:** true
**module:** stand-alone
**File:** `_bmad/agents/toolkit-advisor/toolkit-advisor.agent.yaml`

---

## Validation Findings

### Metadata Validation

**Status:** ✅ PASS

**Checks:**
- ✅ id: `_bmad/agents/toolkit-advisor/toolkit-advisor.md` — kebab-case, matches filename
- ✅ name: `Forge` — clear persona identity name
- ✅ title: `Agentic Toolkit Advisor` — concise functional description
- ✅ icon: `🔥` — single emoji, visually appropriate
- ✅ module: `stand-alone` — correct format (lowercase, hyphenated)
- ✅ hasSidecar: `true` — boolean, matches actual sidecar folder presence
- ✅ sidecar-folder: `toolkit-advisor-sidecar` — present as required when hasSidecar: true
- ✅ sidecar-path: `{project-root}/_bmad/_memory/toolkit-advisor-sidecar/` — correct path format

**Detailed Findings:**

*PASSING:* All 8 metadata fields present and correctly formatted. No naming anti-patterns detected (name ≠ title, id path matches filename).

*WARNINGS:* None

*FAILURES:* None

---

### Persona Validation

**Status:** ✅ PASS (fixed in place)

**Checks:**
- ✅ role: specific capability statement, aligned with menu items, no crossfield contamination
- ✅ identity: establishes character, credibility, and memory-referencing behavior appropriately
- ✅ communication_style: pure voice description — behavioral guidance moved to principles (fixed)
- ✅ principles: first principle correctly activates expert domain knowledge with specific frameworks
- ✅ principles: 5 items within 3-7 recommended range
- ✅ principles: non-obvious philosophy, passes Obvious Test
- ✅ all four fields present and internally consistent

**Detailed Findings:**

*PASSING:* role, identity, principles all fully compliant. First principle follows the "Channel expert [domain] knowledge" pattern exactly. Identity appropriately establishes the sidecar memory-referencing behavior.

*WARNINGS:* None — `communication_style` fixed in place. Behavioral phrase replaced with pure voice shorthand: "Concrete over abstract. Project-specific over generic."

*FAILURES:* None.

---

### Menu Validation

**Status:** ✅ PASS

**hasSidecar:** true

**Checks:**
- ✅ All 5 triggers follow `XX or fuzzy match on command-name` format
- ✅ All 5 descriptions start with matching `[XX]` code
- ✅ No reserved codes used (MH, CH, PM, DA all clear)
- ✅ All 5 trigger codes are unique within agent (SP, RM, CE, BI, SS)
- ✅ All action handlers reference valid `#prompt-id` entries
- ✅ Correct `action:` handler used (stand-alone agent, not exec/module)
- ✅ Menu scope appropriate: 5 items covering full toolkit advisory lifecycle

**Detailed Findings:**

*PASSING:* All 5 menu items (SP, RM, CE, BI, SS) fully compliant. Every `action: '#id'` reference resolves to an existing prompt. No reserved code conflicts.

*WARNINGS:* None

*FAILURES:* None

---

### Structure Validation

**Status:** ✅ PASS

**Configuration:** Agent WITH sidecar

**hasSidecar:** true

**Checks:**
- ✅ Valid YAML syntax (python3 parse confirmed)
- ✅ All required sections present: metadata, persona, critical_actions, prompts, menu
- ✅ Field types correct (booleans are booleans, arrays are arrays, strings are strings)
- ✅ Consistent 2-space indentation throughout
- ✅ hasSidecar: true with sidecar-folder and sidecar-path specified
- ✅ critical_actions: loads memories.md, loads instructions.md, restricts file access
- ✅ All critical_actions use `{project-root}/_bmad/_memory/` path format
- ✅ No compiler-injected content written (no MH/CH/PM/DA, no frontmatter, no XML block)
- ✅ 185 lines — reasonable size for a sidecar agent

**Detailed Findings:**

*PASSING:* Full structural compliance. Correctly follows the division of responsibilities between YAML author and compiler. No duplicate keys, no malformed structures.

*WARNINGS:* None

*FAILURES:* None (Structure)

---

### Sidecar Validation

**Status:** ✅ PASS

**hasSidecar:** true

**Checks:**
- ✅ `metadata.sidecar-folder`: `toolkit-advisor-sidecar` — present and correct
- ✅ Sidecar path format: `{project-root}/_bmad/_memory/toolkit-advisor-sidecar/` — all references use literal `{project-root}`
- ✅ Sidecar folder exists at `_bmad/_memory/toolkit-advisor-sidecar/`
- ✅ `memories.md` present (929 bytes)
- ✅ `instructions.md` present (2318 bytes)
- ✅ No unexpected files in sidecar folder
- ✅ critical_actions: 4 actions (≥3 required), mandatory trio present + valid extra
- ✅ No placeholder text or compiler-injected steps in critical_actions
- ✅ No broken path references

**Detailed Findings:**

*PASSING:* Sidecar folder fully compliant. Both required files (memories.md, instructions.md) present and non-empty. All path references use correct literal `{project-root}` format. The 4th critical_action (situational greeting behavior) is a valid extension of the mandatory trio.

*WARNINGS:* None

*FAILURES:* None (Sidecar)
