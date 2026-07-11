# Kiro Project Onboarding - Reusable Prompt Templates

> Use these prompts to onboard Kiro to any existing project. Adapt the project name and adjust scope as needed.

## Prompt 1: Initiate Onboarding Program

```
I want to onboard you (Kiro) to the [PROJECT_NAME] project as a knowledgeable contributor. 
This means creating a structured steering documentation system that captures project standards, 
patterns, and culture - similar to how a human newcomer reads docs, studies PRs, and internalizes 
"how things are done here."

The goal is a modular memory system stored in .kiro/steering/ with:
1. core-principles.md (always loaded - non-negotiable rules)
2. quick-reference.md (always loaded - common scenarios with step-by-step guidance)
3. pattern-index.md (always loaded - navigation to on-demand modules)

Phase 0: Read existing documentation + representative source code → create initial steering system
Phase 1: Analyze recent PRs → create priority modules
Phase 2: Deep historical analysis → comprehensive modules
Phase 3: Integration and community publication

Start with Phase 0. Discover all existing documentation (README, CONTRIBUTING, wiki, CI configs, 
build scripts), then read representative source files to understand implementation patterns. 
Create the three core steering modules and a gap analysis for subsequent phases.

Store everything under .kiro/steering/ with this structure:
- core-principles.md
- quick-reference.md
- pattern-index.md
- module-architecture.md
- integration-framework.md
- analysis/content-analysis-and-patterns.md
- analysis/phase-0-quality-gate-results.md
- modules/ (empty, for Phase 1+)

Before declaring Phase 0 complete, validate against these quality gates:
1. Source Validation - All sources are authoritative (official docs, maintainer-authored)
2. Pattern Consistency - No contradictory patterns across modules
3. Coverage Baseline - Gaps clearly identified with phase assignments
4. Usability Test - Simulate a realistic contribution scenario using only the guide
```

## Prompt 2: Quick Onboarding (Minimal - Single Session)

```
Onboard yourself to [PROJECT_NAME] by reading all documentation and representative source code, 
then create a .kiro/steering/ directory with three files:

1. core-principles.md - Non-negotiable project rules (architecture, style, process)
2. quick-reference.md - Common scenarios with step-by-step guidance and anti-patterns
3. pattern-index.md - Keyword lookup and gap identification for future enhancement

Sources to analyze:
- All *.md files in repo root and docs/
- Build configs (package.json, pom.xml, Cargo.toml, Makefile, etc.)
- CI/CD configs (.github/workflows/, .gitlab-ci.yml, etc.)
- Contributing guidelines and PR templates
- 3-5 representative source files showing dominant patterns
- Shell/build scripts

Validate by simulating one realistic contribution scenario through the guide.
```

## Prompt 3: Continue Onboarding (Phase 1+)

```
Continue the [PROJECT_NAME] onboarding program. The Phase 0 steering system exists 
at .kiro/steering/. Read the current state:
- pattern-index.md (check gap areas marked for this phase)
- module-architecture.md (check module specifications)
- analysis/phase-0-quality-gate-results.md (check recommendations)

For Phase 1:
1. List the 50 most recent merged PRs with substantial review discussion
2. Analyze maintainer feedback patterns across these PRs
3. Create the priority modules specified in module-architecture.md
4. Update pattern-index.md routing tables
5. Validate with quality gates from QUALITY_ASSURANCE_SPEC.md
6. Store results in analysis/phase-1-quality-gate-results.md
```

## Prompt 4: Source Code Pattern Extraction (Enhancement)

```
Enhance the [PROJECT_NAME] steering system by analyzing representative source code.
Read .kiro/steering/core-principles.md to understand documented patterns, then:

1. Identify 5-10 representative source files that demonstrate core project patterns
2. Read each file and extract IMPLEMENTATION patterns not captured in documentation:
   - Code organization within files
   - Naming conventions in practice
   - Error handling patterns
   - API design patterns
   - Test structure and organization
3. Update quick-reference.md with concrete code pattern examples
4. Add implementation-level guidance to core-principles.md where gaps exist
5. Note any contradictions between documentation and actual code (flag for maintainer clarification)
```

## Prompt 5: Validate and Maintain Steering System

```
Validate the [PROJECT_NAME] steering system against recent project activity.
Read the current steering modules in .kiro/steering/, then:

1. Check the 10 most recent merged PRs - do they follow the documented patterns?
2. Check the 5 most recent maintainer review comments - are there new standards?
3. Identify any guidance that contradicts current practice (staleness detection)
4. Update affected modules with corrections or new patterns
5. Log changes in the module version headers
6. If major changes, re-run usability test with a fresh scenario
```

## Usage Notes

### Adapting to Different Project Types

**JavaScript/TypeScript projects:** Focus on package.json scripts, eslint/prettier configs, testing frameworks (jest/vitest/mocha)

**Rust projects:** Focus on Cargo.toml, clippy lints, module organization, trait patterns

**Python projects:** Focus on pyproject.toml, type annotations, testing patterns (pytest), documentation style

**Infrastructure projects:** Focus on IaC patterns, deployment workflows, environment management

### Scaling Considerations

- **Small projects (<50 PRs):** Prompt 2 (single session) is usually sufficient
- **Medium projects (50-500 PRs):** Full Phase 0 + Phase 1 recommended
- **Large projects (500+ PRs):** Full program (Phase 0-3) provides best results
- **Monorepo projects:** Consider per-package steering modules

### Quality Indicators That Onboarding Worked

- First PR follows project conventions without major feedback
- Kiro correctly identifies which patterns apply to new scenarios
- Maintainer feedback decreases over successive contributions
- Kiro can explain WHY a standard exists, not just WHAT it is
