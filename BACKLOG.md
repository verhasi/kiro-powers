# Backlog

## Next: Enhance JavaParser Power with Hooks

**Goal:** Add Kiro hooks to automate mechanical compliance steps that currently rely on AI memory via steering files.

**Rationale:** JavaParser has an uncommon self-referential build where modifying generator source requires running generators before building. Currently the steering files tell the AI to remind the developer. Hooks make this deterministic and zero-context-cost.

**Planned Hooks:**

### 1. Run Code Generators on Generator Source Change
- **Trigger:** File changed in `javaparser-core-generators/src/**/*.java`
- **Action:** `./run_core_generators.sh`
- **Post-check:** `git diff --exit-code` — notify if generated files changed

### 2. Rebuild Metamodel on AST Node Change
- **Trigger:** File changed in `javaparser-core/src/main/java/com/github/javaparser/ast/**/*.java`
- **Action:** `./run_core_metamodel_generator.sh && ./run_core_generators.sh`
- **Post-check:** `git diff --exit-code` — notify if metamodel/generated files changed

### 3. Verify Pre-PR Compliance
- **Trigger:** User-triggered (before PR submission)
- **Action:** Run generators + checkstyle + verify empty diff
- **Report:** Pass/fail with details

**Design Principle:** 
- Hooks handle mechanical steps that should never be skipped
- Steering handles judgment and reasoning that need intelligence
- Together they provide complete coverage with minimal context cost

**Tasks:**
- [ ] Research Kiro hooks documentation for exact trigger syntax
- [ ] Implement hooks in `javaparser-contributor/hooks/`
- [ ] Test locally with file change triggers
- [ ] Update POWER.md onboarding section to install hooks
- [ ] Update steering files to reference hooks instead of manual reminders

---

## Next Blog Post: "From Steering to Hooks — Automating AI Compliance"

**Angle:** Evolution from "AI remembers the rules" to "hooks enforce the rules automatically"

**Key Points:**
- Steering files = judgment and reasoning (when to think, why it matters)
- Hooks = mechanical compliance (deterministic, never forgotten)
- The combination is more reliable than either alone
- Parallels to human teams: coding standards docs (steering) vs CI checks (hooks)
- Concrete example: JavaParser's self-referential generator build

**Series:** Second post in "AI-Assisted Open Source" series on dev.to

---

## Future Powers (Ideas)

| Project | Why | Complexity |
|---------|-----|-----------|
| Spring Boot contributor | Huge community, strict patterns | Medium |
| Kubernetes contributor | Complex process, many subsystems | High |
| Rust project (e.g. ripgrep) | Different ecosystem, simpler build | Low |

---

*Last updated: July 11, 2026*
