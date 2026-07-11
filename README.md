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
Reusable prompts for onboarding Kiro to any project are in [ONBOARDING_PROMPTS.md](./ONBOARDING_PROMPTS.md).
Quality assurance methodology for validating Powers is in [QUALITY_ASSURANCE.md](./QUALITY_ASSURANCE.md).

## Contributing

We welcome contributions! You can help by:

### Adding a new Power for a project you maintain or contribute to

1. Fork this repository
2. Create a folder for your project: `<project-name>/`
3. Use the [onboarding prompts](./ONBOARDING_PROMPTS.md) or follow the [JavaParser power](./javaparser-contributor/) as a template
4. At minimum include `POWER.md` with frontmatter and core principles
5. Add `steering/` files for on-demand knowledge modules
6. Submit a pull request with a description of the project and your relationship to it

### Improving an existing Power

1. Fork this repository
2. Make changes to the relevant power's files
3. Explain what patterns you're adding and the evidence source (PR, documentation, maintainer feedback)
4. Submit a pull request

### Reporting issues or suggesting improvements

Use [GitHub Issues](https://github.com/verhasi/kiro-powers/issues) for:
- Incorrect or outdated patterns in existing Powers
- Suggestions for new Powers
- Methodology improvements

See [CONTRIBUTING.md](./CONTRIBUTING.md) for detailed guidelines.

## License

MIT
