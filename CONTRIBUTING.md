# Contributing to Kiro Powers

Thank you for your interest in contributing! This repository collects Kiro Powers that help AI assistants become effective contributors to open source projects.

## Types of Contributions

### 1. New Powers for Open Source Projects

The most valuable contribution is adding a Power for a project you actively maintain or contribute to. You understand the project's patterns, culture, and standards better than anyone.

**Requirements for a new Power:**
- You are a maintainer or active contributor to the project
- The Power includes at minimum a `POWER.md` with proper frontmatter
- Patterns are based on official documentation and real contribution experience
- Quality gate: the Power can successfully guide a realistic contribution scenario

**How to create one:**
1. Use the [onboarding prompts](./ONBOARDING_PROMPTS.md) with Kiro
2. Follow the [Kiro Power format](https://kiro.dev/docs/powers/create/)
3. Reference the [JavaParser power](./javaparser-contributor/) as a template
4. Validate by simulating a contribution scenario using only the Power

### 2. Improvements to Existing Powers

If you contribute to a project that already has a Power here and notice:
- Incorrect or outdated guidance
- Missing patterns that caused maintainer feedback
- New project standards not yet captured

Please submit a PR with:
- What pattern you're adding or correcting
- Evidence source (link to PR, maintainer comment, documentation)
- Whether this was discovered through actual contribution experience

### 3. Methodology Improvements

If you have ideas for improving the onboarding methodology itself:
- Better prompt templates
- More efficient phasing strategies
- Quality assurance improvements
- New module types or structures

Open an issue first to discuss the approach.

## Power Quality Standards

### Source Validation
- Patterns must come from official documentation, maintainer-authored content, or verified PR feedback
- No assumptions or guesses — if you're not sure about a pattern, mark it as needing validation

### Pattern Consistency
- No contradictory guidance within a Power
- Technical terms used consistently
- Clear separation between "always true" (POWER.md) and "context-specific" (steering/)

### Actionability
- Guidance must be directive: "Do X" / "Avoid Y"
- Include concrete code examples where relevant
- Anti-patterns should show both wrong and correct approaches

### Evidence-Based
- Each pattern should be traceable to a source (documentation, PR, maintainer feedback)
- Mark confidence levels for patterns based on limited evidence
- Flag areas needing further validation with `[NEEDS VALIDATION]`

## Directory Structure

```
<project-name>/
├── POWER.md                    # Required: frontmatter + core principles + routing
└── steering/                   # Optional: on-demand knowledge modules
    ├── quick-reference.md     # Recommended: common scenarios
    └── <topic>.md             # Additional focused modules
```

## Pull Request Process

1. Fork the repository
2. Create a branch for your changes
3. Make your changes following the quality standards above
4. Test your Power by using it in a real or simulated contribution scenario
5. Submit a PR with:
   - Description of what's added/changed
   - Evidence sources for patterns
   - How you validated the Power works

## Code of Conduct

Be respectful, constructive, and evidence-based. We're building community knowledge — accuracy and helpfulness matter more than volume.

## Questions?

Open an issue or reach out via the links in the README.
