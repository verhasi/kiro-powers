---
name: "javaparser-contributor"
displayName: "JavaParser Open Source Contributor"
description: "Onboarding knowledge for contributing to the JavaParser project - metamodel patterns, code generation, parsing architecture, and contribution workflow"
keywords: ["javaparser", "java parser", "ast", "metamodel", "visitor", "generator", "javacc", "grammar", "parsing", "code generation", "open source", "contribution"]
author: "Istvan Verhas"
---

# JavaParser Contributor Power

This power provides comprehensive onboarding knowledge for contributing to the [JavaParser](https://github.com/javaparser/javaparser) open source project. It captures project standards, architectural patterns, and cultural norms that enable high-quality contributions from the first PR.

## Onboarding

### Step 1: Verify project setup
Ensure the JavaParser project is cloned and builds successfully:
- Verify with: `./mvnw clean install -DskipTests`
- Java 11+ required for building (JDK 8+ for running)
- Bash required for generator scripts

### Step 2: Understand the build pipeline
JavaParser uses JavaParser to build JavaParser. The build is a 6-stage self-referential pipeline:
1. JavaCC → Enables Java parsing
2. Core compile → Builds AST node classes
3. Metamodel gen → Introspects nodes, builds JavaParserMetaModel
4. Core generators → Generates getters/setters/visitors/clone/etc.
5. bnd generator → Produces module export declarations
6. Final build → Produces releasable artifacts

**Critical:** If you change AST nodes, you MUST run:
```bash
./run_core_metamodel_generator.sh   # Stages 1-3
./run_core_generators.sh            # Stages 4-6 (includes spotless:apply)
```

## Core Principles

### Architecture: Metamodel-First
- ALWAYS use metamodel APIs for type checking and metadata access
- NEVER bypass metamodel with raw Java reflection

**Correct:**
```java
node.isInstanceOfMetaModel(JavaParserMetaModel.commentMetaModel)
meta.isAbstract()
```

**Incorrect:**
```java
Comment.class.isAssignableFrom(node.getType())
Modifier.isAbstract(meta.getType().getModifiers())
```

### Code Style (Non-Negotiable)
- 4-space indent (never tabs)
- 120-char line width maximum
- Annotations on separate line above method/type
- Wrapped args aligned to opening parenthesis
- Copyright header required on all new files (see steering/quick-reference.md)

### Contribution Process
- **One PR = One concern.** No exceptions.
- All functional changes require tests (BDD or JUnit)
- Test behavioral contracts, not implementation details
- Before every PR: `./run_core_metamodel_generator.sh && ./run_core_generators.sh` → git diff must be empty

### Generated Code
- Methods marked `@Generated("...")` are auto-generated — NEVER edit manually
- Use `assertNotNull()` from `com.github.javaparser.utils.Utils` (not java.util.Objects)
- Setters use `notifyPropertyChange()` + `setAsParentNodeOf()` for observer and tree integrity

### Parsing Architecture
- Parser NEVER throws exceptions for invalid input — Problems are collected in ParseResult
- Grammar file: `javaparser-core/src/main/javacc/java.jj` (~6,155 lines)
- Language level validators: negative in Java1_0Validator, remove restriction in appropriate version
- Regenerate parser after grammar changes: `./mvnw javacc:javacc`

### Node Class Ordering
`MetaModelGenerator.ALL_NODE_CLASSES` — parent classes MUST appear before child classes. Breaking this causes silent metamodel corruption.

## When to Load Steering Files
- Common scenarios, checklists, build commands, anti-patterns → `quick-reference.md`
- Working on code generators or visitors → `generator-development.md` [Phase 1]
- Creating or reviewing pull requests → `pr-workflow.md` [Phase 1]
- Adding/modifying AST nodes or metamodel → `metamodel-patterns.md` [Phase 2]
- Writing or fixing tests → `testing-standards.md` [Phase 2]
- Making architectural decisions → `architecture-decisions.md` [Phase 2]
