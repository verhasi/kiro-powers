# Edge Cases Review — Format Guide

> This file defines the format that the orchestrator uses to present edge cases for human decision.
> Generated per use case, after Red-Test signals COMPLETE.

## File Location

`edge-cases-review-[uc-slug].md` in project root — one file per use case, deleted after decisions are written to `usecases.md`.

Examples:
- `edge-cases-review-uc1-basic-addition.md`
- `edge-cases-review-uc2-flexible-delimiters.md`

## Format

```markdown
# Edge Cases Review: [Use Case Name]

> These edge cases were identified during TDD but are NOT covered by the current spec.
> The spec is silent or ambiguous about each case below.
> **Your decision is required** before the TDD squad proceeds.

## How to Decide

Each edge case has TWO parts to your decision:

### Part 1: Is this relevant now?

| Action | Meaning |
|--------|---------|
| **ACCEPT** | Yes, we need to define this behavior — see Part 2 |
| **REJECT** | Explicitly out of scope — no action needed |
| **DEFER** | Valid concern but not now — tracked for future iteration |
| **ESCALATE** | You cannot decide alone — needs input from [business/team lead/architect] |

### Part 2: What should the behavior be? (only if ACCEPT)

The review lists possible behaviors (A, B, C...) but you are NOT limited to those options.
You can:
- **Pick one** of the listed options: "ACCEPT — option B"
- **Define your own**: "ACCEPT — trim whitespace and parse normally"
- **Modify a listed option**: "ACCEPT — like option A but only for negative numbers"

The behavior you specify becomes the new spec text that tests will be written against.

---

## Edge Case 1: [Short descriptive title]

**What the spec says:** "[exact quote or 'nothing']"

**What's ambiguous:** [plain-language explanation of what's undefined]

**Possible behaviors (suggestions, not exhaustive):**
- A) [option — e.g., "throw an exception"]
- B) [option — e.g., "ignore silently"]  
- C) [option — e.g., "treat as zero"]

---

**Part 1 — Relevant?** ACCEPT / REJECT / DEFER / ESCALATE: _______________

**Part 2 — Desired behavior (if ACCEPT):** _______________
(pick from above, modify, or write your own)

**Rationale / escalation target:** _______________

---

## Edge Case 2: ...

(repeat pattern)
```

## Key Principles

1. **Human-readable first** — no agent jargon, no code references unless essential
2. **One decision per case** — clear action required, no bundling
3. **Escalation is explicit** — name who should decide if you can't
4. **Traceability** — always quote what the spec says (or doesn't say)
5. **Per use case** — not accumulated across all use cases
