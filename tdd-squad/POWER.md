---
name: "tdd-squad"
displayName: "TDD Squad - Autonomous Test-Driven Development"
description: "5 specialized AI agents that implement features autonomously using strict Red-Green-Blue TDD workflow with context isolation"
keywords: ["tdd", "test driven", "red green blue", "refactor", "testing", "junit", "jest", "vitest", "unit test", "clean code", "solid", "implementation", "feature"]
author: "Istvan Verhas"
---

# TDD Squad

An autonomous squad of 5 specialized AI agents that implement features using strict Test-Driven Development. Each agent has laser focus and enforced context isolation — Green can't cheat by reading specs, Blue-Clean doesn't know what the code does business-wise.

## Onboarding

### Step 1: Validate prerequisites
- A test framework must be configured in the project (JUnit, Jest, Vitest, etc.)
- The project must compile/build successfully before starting

### Step 2: Install agent configurations
Create the following agent configuration files in the project:

Create `.kiro/agents/red-contract.json` with content from this power's `agents/red-contract.json`.
Create `.kiro/agents/red-test.json` with content from this power's `agents/red-test.json`.
Create `.kiro/agents/green.json` with content from this power's `agents/green.json`.
Create `.kiro/agents/blue-intent.json` with content from this power's `agents/blue-intent.json`.
Create `.kiro/agents/blue-clean.json` with content from this power's `agents/blue-clean.json`.

### Step 3: Prepare your feature
Use `/plan` to create:
- `requirements.md` — What you want to build
- `usecases.md` — Ordered list of use cases

Approve the use cases (intent validation), then ask Kiro to execute the TDD squad.

## How to Use

After setup, tell Kiro:

> "Implement the feature using the TDD squad workflow. Start with use case 1 from usecases.md."

Or for the full autonomous run:

> "Execute the TDD squad for all use cases in usecases.md."

## Context Isolation Rules

| Agent | requirements.md | usecases.md | contract | tests | source code |
|-------|:-:|:-:|:-:|:-:|:-:|
| red-contract | ❌ | ✅ (current UC) | ❌ | ❌ | ❌ |
| red-test | ❌ | ✅ (current UC) | ✅ | ✅ | ❌ |
| green | ❌ | ❌ | ❌ | ✅ | ✅ |
| blue-intent | ❌ | ✅ (current UC) | ✅ | ✅ | ✅ |
| blue-clean | ❌ | ❌ | ❌ | ✅ | ✅ |

## When to Load Steering Files
- Orchestrating the TDD squad workflow → `tdd-squad.md`
