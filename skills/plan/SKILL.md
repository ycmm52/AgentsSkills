---
name: plan
description: Requirements clarification and approval-gated planning. Use when starting any task that needs clear understanding before coding - analyzes user requirements, asks clarifying questions, presents implementation plan with before/after comparisons and flow diagrams, and blocks all code changes until explicit user approval. Triggers on "plan this", "create a plan", "before we start coding", or when requirements seem unclear or complex.
---

# Plan

**Announce at start:** "I'm using the plan skill to clarify requirements and create an implementation plan."

## Overview

This skill enforces a "plan-first" workflow: review requirements, clarify ambiguities, present an implementation plan, and wait for explicit user approval before any code changes.

**Critical constraint:** Use ONLY read-only tools (Read, Grep, Glob, SemanticSearch) during planning. Do NOT create, modify, or delete any files until the user explicitly approves the plan.

## Planning Workflow

### Phase 1: Analyze Requirements

Review the user's requirements. Identify:
- Core objectives and deliverables
- Technical scope and boundaries
- Implicit assumptions that need validation
- Dependencies on existing code or systems

### Phase 2: Ask Clarifying Questions

Execute this phase for EVERY task. Use this checklist:

**Clarification Checklist:**

- [ ] Edge cases and error handling expectations - Asked or N/A documented
- [ ] Performance or scalability requirements - Asked or N/A documented
- [ ] Integration points with existing systems - Asked or N/A documented
- [ ] User experience or interface requirements - Asked or N/A documented
- [ ] Testing expectations - Asked or N/A documented

**For each item:**

1. Evaluate if clarification is needed
2. If needed: Ask using AskQuestion tool for multiple-choice, or ask directly for open-ended
3. If not needed: Document why it's clear (e.g., "Edge cases: N/A - pure data transformation")

**Output:** List of clarifications obtained or documented N/A reasons.

### Phase 3: Research Codebase

Use read-only tools to understand the current codebase:
- Search for related files and existing implementations
- Identify files that will need modification
- Understand current patterns and conventions
- Map dependencies and integration points

### Phase 4: Review for Consistency

Based on the proposed changes identified in Phase 3, analyze what other parts of the codebase should be updated to maintain consistency:

- **Pattern consistency:** If modifying a pattern used elsewhere, identify all instances that should follow the same change
- **Naming conventions:** If renaming or adding new conventions, find related code that should align
- **API contracts:** If changing interfaces, identify all callers and implementers affected
- **Documentation:** If behavior changes, identify docs, comments, or examples needing updates
- **Tests:** If modifying functionality, identify test files beyond unit tests (integration, e2e) that may need updates
- **Configuration:** If adding new config options, check for similar patterns in other config files

**Output:** List of additional files/areas requiring changes for consistency, grouped by category.

### Phase 5: Present Implementation Plan

Create a structured plan with ALL sections below (no exceptions):

#### 5.1 High-Level Summary (MANDATORY)

Provide a concise summary of what will be changed:

- Overall approach and architecture decisions
- Key components affected
- Expected impact on the system

#### 5.2 Before/After Comparison (MANDATORY)

Create a comparison table showing behavioral differences:

| Scenario | Before | After |
|----------|--------|-------|
| [Case 1] | Current behavior | Proposed behavior |
| [Case 2] | Current behavior | Proposed behavior |

For simple changes, the table may have fewer rows but MUST exist.

#### 5.3 ASCII Flow Chart (MANDATORY)

**ALWAYS use ASCII flow charts. Do NOT use mermaid diagrams.**

Include an ASCII diagram showing control flow, data flow, component relationships, or sequence of operations.

##### Few-Shot Examples: ASCII Flow Charts

**CORRECT (Simple linear flow):**

```
Input → Validate → Process → Output
```

**CORRECT (Branching flow):**

```
┌─────────────┐
│   Request   │
└──────┬──────┘
       │
       ▼
┌─────────────┐     ┌─────────────┐
│  Validate   │────▶│   Error     │
└──────┬──────┘ no  └─────────────┘
       │ yes
       ▼
┌─────────────┐
│  Response   │
└─────────────┘
```

**INCORRECT (Using mermaid):**

```
graph TD
    A[Start] --> B[Process]
```

❌ WRONG: Do NOT use mermaid syntax. Use ASCII box drawing: ┌─┐ └─┘ │ ▶ ▼

#### 5.4 Files to Modify/Create (MANDATORY)

List all files that will be touched:

- **Create:** New files with their purpose
- **Modify:** Existing files with brief description of changes
- **Delete:** Files to be removed (if any)

#### 5.5 Implementation Tasks (MANDATORY)

**REQUIRED FIRST STEP:** Invoke test-driven-development skill to learn TDD best practices.

> **Agent action:** Read the test-driven-development skill NOW before writing any tasks below.
> This ensures proper Red-Green-Refactor methodology, correct test structure, and avoidance of common TDD pitfalls.

After reading test-driven-development skill, break down implementation into bite-sized tasks.

**Task Granularity:** Each step is one action (2-5 minutes)

**Complete Code Requirement:** Write EXACT code in the plan. Never write "add validation" or "implement logic" - write the actual code.

**Task Structure:**

```
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write failing test**

```python
# EXACT test code here
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Verify test fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
# EXACT implementation code here
def function(input):
    return expected
```

**Step 4: Verify test passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS
```

### Phase 6: Request Approval

After presenting the plan, explicitly state:

---

**This plan is ready for your review. I will NOT make any code changes until you approve.**

To proceed, please:
- Reply "approved" or "go ahead" to start implementation
- Ask questions if anything is unclear
- Request modifications to the plan if needed

---

### Phase 7: Execution Handoff

**Announce:** "Plan approved. Ready for execution."

**REQUIRED:** Before executing any task, invoke test-driven-development skill.

**Execution Options:**

**Option 1: Subagent-Driven (this session)**
- Dispatch fresh subagent per task
- Review between tasks
- Fast iteration
- Stay in current session

**Option 2: Parallel Session (separate)**
- Open new session in worktree
- Batch execution with checkpoints
- Use executing-plans skill

**Ask user:** "Which execution approach?"

## Important Constraints

- Use ONLY read-only tools during planning phase (Phases 1-5)
- Do not create, modify, or delete any files until user approves
- Do not execute any commands that change system state during planning
- Wait for explicit approval before any implementation
- ALL code changes require TDD - invoke test-driven-development skill
