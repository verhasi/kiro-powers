# TDD Squad Orchestration

> This steering file instructs the main agent to orchestrate the TDD squad for feature implementation.

## When to Activate

Use the TDD squad when implementing features that have a `usecases.md` file (produced by /plan).

## Workflow

### Prerequisites
- `requirements.md` exists (human-authored or /plan output)
- `usecases.md` exists with ordered list of use cases (from /plan, human-approved)

### Execution: For Each Use Case

#### Step 1: Red-Contract (once per use case)
Invoke `red-contract` agent with:
- **Context:** The current use case text from usecases.md
- **Task:** "Design the API contract/interfaces for this use case: [use case text]"
- **Output:** Contract file (OpenAPI yaml or interface definitions)

#### Step 2: TDD Loop (repeats until Red-Test says COMPLETE)

**Red-Test round:**
Invoke `red-test` agent with:
- **Context:** Current use case + contract + path to existing test files
- **Task:** "Review the use case and existing tests. Write ONE new failing test, or respond COMPLETE if all behaviors are tested."
- **Exit condition:** Response contains "COMPLETE" → move to next use case

**Green round:**
Invoke `green` agent with:
- **Context:** Path to test files + source files + type definitions ONLY
- **Task:** "Make the failing test(s) pass with the simplest possible implementation."
- **DO NOT pass:** requirements.md, usecases.md, or contract files

**Blue-Intent round:**
Invoke `blue-intent` agent with:
- **Context:** Use case text + contract + test files + source code
- **Task:** "Verify the implementation matches the use case intent. Fix specification gaming if detected."

**Blue-Clean round:**
Invoke `blue-clean` agent with:
- **Context:** Test files + source code ONLY
- **Task:** "Refactor for clean code and architecture. Tests must still pass."
- **DO NOT pass:** requirements.md, usecases.md, or contract files

**Loop back to Red-Test.**

### Completion
When all use cases have Red-Test signaling COMPLETE, the feature is implemented.

## Context Isolation Rules

| Agent | requirements.md | usecases.md | contract | tests | source code |
|-------|:-:|:-:|:-:|:-:|:-:|
| red-contract | ❌ | ✅ (current UC) | ❌ | ❌ | ❌ |
| red-test | ❌ | ✅ (current UC) | ✅ | ✅ | ❌ |
| green | ❌ | ❌ | ❌ | ✅ | ✅ |
| blue-intent | ❌ | ✅ (current UC) | ✅ | ✅ | ✅ |
| blue-clean | ❌ | ❌ | ❌ | ✅ | ✅ |

## Subagent Pipeline Structure

```
For each use case:
  Sequential: red-contract
  Loop (until COMPLETE):
    Sequential: red-test → green → blue-intent → blue-clean
```

## Error Handling

- If Green cannot make tests pass after 3 attempts: escalate to human
- If Blue-Intent detects unfixable spec gaming: flag to human with explanation
- If tests break during Blue-Clean refactoring: revert Blue-Clean changes, continue loop
