# Kiro Powers - Open Source Project Onboarding

A collection of [Kiro Powers](https://kiro.dev/powers/) that provide AI onboarding knowledge for open source projects. Each power captures project standards, architectural patterns, and cultural norms to enable high-quality contributions.

## Available Powers

| Power | Project | Status |
|-------|---------|--------|
| [javaparser-contributor](./javaparser-contributor/) | [JavaParser](https://github.com/javaparser/javaparser) | Phase 0 ✅ |

## What Are These Powers?

When contributing to an open source project, you need to understand:
- Architecture and design patterns
- Code style and conventions
- Build and CI processes
- Review culture and expectations
- Testing standards

These powers package that knowledge so Kiro can guide contributions as if it's an experienced project contributor.

## Installation

In Kiro IDE: **Powers panel** → **Add Custom Power** → **Import power from GitHub** → enter this repo URL with the power path.

Or install directly from a power's folder.

## Creating Powers for New Projects

Each power follows the [Kiro Power format](https://kiro.dev/docs/powers/create/):

```
<project-name>/
├── POWER.md          # Frontmatter + core principles + steering routing
└── steering/         # On-demand knowledge modules
    ├── quick-reference.md
    └── <topic>.md
```

### Methodology

Powers are created through a phased onboarding process:
- **Phase 0:** Read existing documentation + representative source code → POWER.md + quick-reference.md
- **Phase 1:** Analyze recent PRs → priority topic modules
- **Phase 2:** Deep historical analysis → comprehensive modules
- **Phase 3:** Community validation and publication

See the [JavaParser power](./javaparser-contributor/) as a reference implementation.

## License

MIT
