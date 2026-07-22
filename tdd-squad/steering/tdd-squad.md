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
- **Context:** Current use case text + contract + path to existing test files
- **Task:** "Here is the use case spec and the existing tests. Apply your systematic boundary analysis. Decide: write ONE new failing test, or respond COMPLETE if all spec-derivable behaviors are tested."
- **CRITICAL:** Do NOT tell Red-Test which test to write next. Do NOT hint at remaining behaviors. Do NOT pre-decide completeness. Do NOT list "already tested behaviors." Red-Test owns this decision autonomously based on its own analysis.
- **Exit condition:** Response contains "COMPLETE" → move to next use case

**Quality gate (orchestrator):**
After Red-Test completes, verify ALL test methods have the required traceability javadoc:
```
/**
 * Feature: [feature name]
 * Use Case: [use case name]
 * Spec: "[exact quote]"
 */
```
If any test is missing traceability, send back to Red-Test with instruction: "Add missing traceability javadoc to all tests that lack it. Every test must have Feature, Use Case, and Spec quote."

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

**Git commit (orchestrator):**
After Blue-Clean completes and tests pass, commit all changes:
- Message format: `TDD [UC name] Round N: [one-line summary of what was tested and implemented]`
- Stage only src/ files (tests + production code)
- This creates full traceability in git history

**Loop back to Red-Test.**

### Edge Cases Review (after each use case)

When Red-Test signals COMPLETE for a use case:

1. **Check** if `edge-cases-review-[uc-slug].md` exists for this use case (e.g., `edge-cases-review-uc1-basic-addition.md`)
2. If yes → **reformat** the file according to `steering/edge-cases-review-format.md` and **present to human**
3. If no edge cases → proceed to next use case

#### Interaction Model (hybrid)

The orchestrator presents ALL edge cases at once in a readable format, then **stops and waits** for the human to respond. The human can:

- Decide all at once: "ACCEPT 1 (throw exception), REJECT 2, DEFER 3, ESCALATE 4 to team lead"
- Decide one by one in conversation
- Ask clarifying questions before deciding
- Mix decisions and escalations freely

The orchestrator does NOT ask one-by-one interactively — it presents the full picture, then interprets whatever response format the human uses.

**After all decisions are collected**, the orchestrator:
1. Writes decisions to `usecases.md` (format below)
2. Deletes the `edge-cases-review-[uc-slug].md` file (decisions are now in usecases.md)
3. Proceeds with post-decision TDD rounds for ACCEPT cases

**Human decides** for each edge case: ACCEPT / REJECT / DEFER / ESCALATE

**Orchestrator writes decisions back to `usecases.md`** under the relevant use case as a subsection:

```markdown
### Use Case 1: Basic Addition

(original spec text...)

#### Edge Case Decisions

##### Trailing delimiter (ACCEPT)
> **From edge-cases-review.md:** "Spec does not define behavior for inputs ending with a delimiter (e.g., '1,2,')"
> **Possible behaviors:** A) throw exception B) ignore trailing C) treat as zero
> **Decision:** ACCEPT — throw IllegalArgumentException
> **Rationale:** Consistent with strict parsing philosophy

##### Whitespace around numbers (REJECT)
> **From edge-cases-review.md:** "Spec does not define whether ' 1 , 2 ' is valid"
> **Decision:** REJECT — out of scope, inputs are always well-formed
> **Rationale:** Spec explicitly shows clean examples, no trimming needed

##### Very large numbers (DEFER)
> **From edge-cases-review.md:** "Spec does not define upper bound for numbers"
> **Decision:** DEFER — track for v2
> **Rationale:** No production requirement yet

##### Unicode delimiters (ESCALATE)
> **From edge-cases-review.md:** "Custom delimiter syntax may receive unicode characters"
> **Decision:** ESCALATE to Tech Lead
> **Rationale:** Internationalization decision beyond developer scope
```

#### Post-Decision TDD Round

After decisions are written to `usecases.md`:

1. **For each ACCEPT decision:** Run a new TDD loop (Red-Test → Green → Blue-Intent → Blue-Clean) using the accepted edge case as additional spec. Red-Test can now quote the decision text.
2. **For REJECT:** No action — explicitly documented as out of scope.
3. **For DEFER:** No action now — remains in usecases.md for future reference.
4. **For ESCALATE:** **STOP.** Do not proceed to next use case. Wait for escalation resolution. When resolved, update the decision to ACCEPT or REJECT and continue.

Only after all edge cases are resolved (no ESCALATE pending) → proceed to next use case.

### Completion

When all use cases have:
- Red-Test signaling COMPLETE
- All edge cases decided (no pending ESCALATE)
- All ACCEPT decisions implemented via TDD loops

The feature is implemented. No gap between intention and implementation — ambiguity is surfaced, decided by humans, and traced in the spec.

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
