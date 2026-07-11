# JavaParser Quick Reference

> This module is ALWAYS loaded. Common scenarios with step-by-step guidance.

## Common Contribution Scenarios

### Scenario: Bug Fix (Most Common)

```
1. Identify the bug and write a failing test
2. Implement the fix in the appropriate module
3. Verify the test now passes
4. Run: ./run_core_generators.sh
5. Verify: git diff shows no unexpected changes
6. Run: ./mvnw clean test
7. Commit with message: "Fix <description>\n\nFixes #<issue>"
8. Create PR with focused scope
```

### Scenario: Generator Modification

```
1. Identify the generator in javaparser-core-generators/
2. Use ONLY metamodel APIs (node.isInstanceOfMetaModel(), meta.isAbstract())
3. Run: ./run_core_generators.sh
4. Check git diff on generated files:
   - Changes present → Verify they're correct
   - No changes → Generator was already correct (or issue is elsewhere)
5. Commit generator changes SEPARATELY from generated code changes
6. Run: ./mvnw clean test
```

### Scenario: Adding New AST Node (Complex - 15 Steps)

```
1.  Add node skeleton with @AllFieldsConstructor
2.  Add to MetaModelGenerator.ALL_NODE_CLASSES (AFTER parent classes)
3.  Run: ./run_core_metamodel_generator.sh
4.  Run: ./run_core_generators.sh (may fail)
5.  Fix PrettyPrintVisitor + DefaultPrettyPrinterVisitor (add visit methods)
6.  Re-run: ./run_core_generators.sh (should succeed)
7.  Clean up: selective git add of relevant changes only
8.  Add placeholder: ConcreteSyntaxModel.java → concreteSyntaxModelByClass.put(...)
9.  Add AST tests (they will fail until grammar is added)
10. Add grammar rules to java.jj → run: ./mvnw clean javacc:javacc
11. Add symbol solver support + tests
12. Add PrettyPrinter tests + final implementation
13. Add ConcreteSyntaxModel + LexicalPreservingPrinter tests
14. Add validators (negative in Java1_0Validator, remove in relevant version)
15. Final: ./mvnw clean test && ./mvnw checkstyle:check
```

### Scenario: Adding Field to Existing Node

```
1. Add field + update @AllFieldsConstructor
2. Run: ./run_core_generators.sh (should succeed - node already exists)
3. Clean up generated changes
4. Update tests, pretty printer, CSM as needed
5. Run: ./mvnw clean test
```

### Scenario: Modifying Parser Grammar (java.jj)

```
1. Understand the existing grammar structure around your change area
2. Add/modify productions in javaparser-core/src/main/javacc/java.jj
3. Use LOOKAHEAD where grammar is ambiguous (check JavaCC docs)
4. Regenerate parser: ./mvnw javacc:javacc
5. Watch for "Warning: Choice conflict in..." (means LOOKAHEAD needed)
6. Fix compilation errors in GeneratedJavaParser actions
7. Write parsing tests that verify correct AST construction
8. Add validator if this is a version-specific feature:
   - Negative validator in Java1_0Validator (rejects new syntax)
   - Remove restriction in the appropriate version validator
9. Run full test suite: ./mvnw clean test
```

### Scenario: Modifying Metamodel

```
1. Make changes to node classes in javaparser-core
2. Ensure core compiles: ./mvnw -pl javaparser-core clean compile
3. Run: ./run_core_metamodel_generator.sh
4. Run: ./run_core_generators.sh
5. Verify changes make sense
6. Run full test suite
```

## Build Commands Quick Reference

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `./mvnw clean install` | Full build, skip tests | First setup, dependency issues |
| `./mvnw clean install -DskipTests` | Full build, no tests | Quick rebuild |
| `./mvnw clean test` | Run all tests | Before PR submission |
| `./mvnw clean test -P AlsoSlowTests` | Full test suite | Final validation |
| `./mvnw javacc:javacc` | Generate parser from grammar | After java.jj changes |
| `./mvnw checkstyle:check` | Validate code style | Before PR submission |
| `./mvnw spotless:apply` | Auto-format code | Quick formatting fix |
| `./run_core_metamodel_generator.sh` | Rebuild metamodel | After node structure changes |
| `./run_core_generators.sh` | Run all generators + format | Before every PR |

## Pre-Commit Checklist

- [ ] Changes are focused on single concern
- [ ] No unrelated formatting or improvement changes included
- [ ] Tests written for functional changes
- [ ] Test names accurately describe what they test
- [ ] Assertions test behavioral contracts
- [ ] Used metamodel APIs (not raw reflection)
- [ ] Copyright header on new files

## Pre-PR Checklist

- [ ] `./run_core_metamodel_generator.sh && ./run_core_generators.sh` → no diff
- [ ] `./mvnw clean test` passes
- [ ] `./mvnw checkstyle:check` passes
- [ ] PR title is clear and concise (<70 chars)
- [ ] PR references issue number (Fixes #XXXX)
- [ ] Single concern - no unrelated changes
- [ ] Commit messages explain WHY not just WHAT

## Common Mistakes to Avoid

### ❌ Bypassing Metamodel
```java
// WRONG - raw reflection
if (Comment.class.isAssignableFrom(node.getType())) { ... }

// RIGHT - metamodel API
if (node.isInstanceOfMetaModel(JavaParserMetaModel.commentMetaModel)) { ... }
```

### ❌ Testing Implementation Details
```java
// WRONG - tests specific value
assertEquals(0, visitor.visit(node1, null));
assertEquals(0, visitor.visit(node2, null));

// RIGHT - tests behavioral contract
assertEquals(visitor.visit(node1, null), visitor.visit(node2, null));
```

### ❌ Copy-Paste Test Errors
```java
// WRONG - name says equals but tests hashCode
@DisplayName("NoCommentEqualsVisitor should...")  // But this IS the hashCode test!
void testNoCommentEquals() { ... }               // Wrong method name too!

// RIGHT - accurate naming
@DisplayName("NoCommentHashCodeVisitor should produce equal hashes...")
void testNoCommentHashCode() { ... }
```

### ❌ Scope Creep in PRs
```
// WRONG - mixed concerns
PR: "Fix MarkdownComment equality + improve Spotless config + fix typo in README"

// RIGHT - focused PRs
PR #1: "Fix MarkdownComment equality in NoComment visitors"
PR #2: "Improve Spotless Maven plugin configuration"
PR #3: "Fix typo in README"
```

### ❌ Skipping Generator Run
```bash
# WRONG - forgot generators
git add . && git commit && git push

# RIGHT - always run before pushing
./run_core_generators.sh
git diff  # Verify empty
git add . && git commit && git push
```

### ❌ Wrong Node Ordering in MetaModelGenerator
```java
// WRONG - child before parent
ALL_NODE_CLASSES.add(LiteralStringExpr.class);  // Child
ALL_NODE_CLASSES.add(LiteralExpr.class);        // Parent - TOO LATE!

// RIGHT - parent before child
ALL_NODE_CLASSES.add(LiteralExpr.class);        // Parent first
ALL_NODE_CLASSES.add(LiteralStringExpr.class);  // Then child
```

## Decision Trees

### "Should I run the metamodel generator?"
```
Did you change node class structure (add/remove fields/nodes)?
├── YES → Run ./run_core_metamodel_generator.sh THEN ./run_core_generators.sh
└── NO  → Did you change generator code?
    ├── YES → Run ./run_core_generators.sh only
    └── NO  → Run ./mvnw spotless:apply (formatting only)
```

### "Where does my code belong?"
```
Is it AST structure, parsing, or visitor logic?
├── YES → javaparser-core
└── NO  → Is it code that generates boilerplate for AST nodes?
    ├── YES → javaparser-core-generators
    └── NO  → Is it type resolution or symbol lookup?
        ├── YES → javaparser-symbol-solver-core
        └── NO  → Is it a test?
            ├── YES → Corresponding *-testing module
            └── NO  → Ask for guidance
```

### "My CI formatting check failed - what do I do?"
```
1. Go to failed job output
2. Scroll to bottom of "Generate code and format" tab
3. Copy the diff output (excluding [INFO] and Error lines)
4. Save to file: /tmp/style.patch
5. Apply: git apply /tmp/style.patch
6. Review changes - do they overwrite your logic?
   ├── YES → Your code should be generated, not manual. Fix approach.
   └── NO  → git add + commit + push
```

## Key File Locations

| Purpose | Path |
|---------|------|
| AST nodes | `javaparser-core/src/main/java/com/github/javaparser/ast/` |
| Visitors | `javaparser-core/src/main/java/com/github/javaparser/ast/visitor/` |
| Generators | `javaparser-core-generators/src/main/java/com/github/javaparser/generator/` |
| Metamodel | `javaparser-core/src/main/java/com/github/javaparser/metamodel/` |
| Grammar | `javaparser-core/src/main/javacc/java.jj` |
| Tests (core) | `javaparser-core-testing/src/test/java/` |
| Tests (BDD) | `javaparser-core-testing-bdd/src/test/java/` |
| Symbol solver | `javaparser-symbol-solver-core/src/main/java/` |
| IDE settings | `dev-files/JavaParser-idea.xml`, `dev-files/JavaParser-eclipse.xml` |
| CSM | `javaparser-core/src/main/java/.../printer/ConcreteSyntaxModel.java` |
| Validators | `javaparser-core/src/main/java/.../ast/validator/language_level_validations/` |

## Source Code Implementation Patterns

### Test File Structure
```java
package com.github.javaparser.ast.expr;

import static com.github.javaparser.StaticJavaParser.parseExpression;
import static org.junit.jupiter.api.Assertions.assertEquals;

class XxxTest {                                         // Package-private (no public)

    @Test
    void descriptiveTestName() {                        // Package-private methods
        Expression expr = parseExpression("x -> y");    // Parse → Act → Assert
        assertEquals("expected", expr.toString());
    }

    // Helper methods for language-level-specific parsing:
    private <T> T parseAtLevel(String code, LanguageLevel level) {
        JavaParser parser = new JavaParser(
            new ParserConfiguration().setLanguageLevel(level));
        ParseResult<Statement> result = parser.parse(ParseStart.STATEMENT, provider(code));
        assertTrue(result.isSuccessful(), result.toString());
        return (T) result.getResult().get();
    }
}
```

### Generator Field Iteration Pattern
```java
for (PropertyMetaModel field : node.getAllPropertyMetaModels()) {
    final String getter = field.getGetterMethodName() + "()";
    if (field.getNodeReference().isPresent()) {
        if (field.isOptional()) { /* Handle Optional<Node> */ }
        else if (field.isNodeList()) { /* Handle NodeList<Node> */ }
        else { /* Handle plain Node */ }
    } else {
        Class<?> type = field.getType();
        if (type.equals(boolean.class)) { ... }
        else if (type.equals(int.class)) { ... }
        else { /* .hashCode() or .equals() */ }
    }
}
```

### Utility Imports Convention
```java
// Use static imports:
import static com.github.javaparser.utils.Utils.assertNotNull;
import static com.github.javaparser.utils.CodeGenerationUtils.f;
import static com.github.javaparser.StaticJavaParser.parseExpression;
// NOT qualified form: Utils.assertNotNull(x) or String.format(...)
```
