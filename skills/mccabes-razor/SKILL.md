---
name: mccabes-razor
description: Shape and simplify decision-heavy control flow while writing, extending, reviewing, or refactoring code. Use cyclomatic and cognitive complexity as diagnostic signals without optimizing for a score. Skip trivial linear code.
---

# McCabe's Razor

Make branching code easier to read, test, and change. Remove accidental
decisions while keeping essential behavior explicit.

Cyclomatic complexity estimates independent execution paths. Cognitive
complexity highlights nesting and interrupted linear flow. Treat both as clues,
not proof that a design is good or bad.

## Measure with context

- Follow the repository's configured analyzer and threshold. Tools count some
  language constructs differently.
- Check existing project commands and analysis configuration before suggesting
  how to measure. Do not add a dependency or quality gate unless requested.
- Use the same analyzer, rule configuration, and version for the baseline and
  final measurement. Keep the baseline output before editing.
- If no analyzer is configured but a suitable tool is already available, use
  it in report-only mode and name the tool and rule. Do not infer a threshold
  from its defaults.
- Evaluate individual functions or methods. Aggregate scores mostly describe
  how much logic exists, not whether it is well shaped.
- If no suitable analyzer is available, do not invent a score. Look for
  nesting, repeated conditions, compound predicates, multiple exits, and
  exception paths.
- Do not impose a universal threshold. Compare with nearby code and avoid making
  touched code harder to follow.

Common analyzer names to check before searching elsewhere:

- JavaScript / TypeScript: ESLint `complexity`; SonarJS
  `cognitive-complexity` when configured.
- Python: Radon `cc`.
- Go: `gocyclo`.
- Java: PMD or Checkstyle `CyclomaticComplexity`; Sonar when configured.
- Kotlin: detekt `CyclomaticComplexMethod` and `CognitiveComplexMethod`.
- Ruby: RuboCop `Metrics/CyclomaticComplexity` and
  `Metrics/PerceivedComplexity`.
- Swift: SwiftLint `cyclomatic_complexity`.
- C, C++, or a polyglot fallback: Lizard.

This list is a shortcut, not a dependency list. Prefer the project's existing
tool and configuration. Do not install an analyzer unless requested.

## While writing code

Before implementing nontrivial branching logic, identify its inputs, outcomes,
invariants, and where each decision belongs.

- Normalize inconsistent input once at the boundary.
- Use guard clauses when they keep the main path linear.
- Keep a direct conditional when it expresses a small local choice clearly.
- Use data or a lookup when stable values map directly to stable outcomes.
- Extract an operation only when it has one coherent purpose or owns a repeated
  rule.
- Keep each rule in one place instead of copying its condition across callers.
- Introduce an abstraction only when it makes the code easier to understand,
  not merely to reduce a score.

After writing or extending decision-heavy code, inspect the touched functions,
run the relevant tests, and use the selected analyzer when one is available.

## While refactoring

Treat a complexity warning as a reason to understand the decisions, not an
instruction to split the function mechanically.

Preserve observable behavior and public interfaces unless the task requires a
change.

1. Read the whole function, its callers, and its tests.
2. Establish a baseline with existing tests and, when available, the selected
   analyzer. Record each hotspot's score before editing. If behavior is
   unprotected, add the smallest useful characterization check.
3. Name what each decision protects or produces. Separate necessary choices
   from duplication, inconsistent inputs, flags, repeated work, and misplaced
   rules.
4. Apply the smallest change that removes or localizes accidental decisions:
   - remove unreachable or duplicate branches;
   - merge conditions with the same outcome;
   - flatten exceptional paths with guards;
   - normalize once instead of branching repeatedly;
   - replace repeated searches with a suitable data structure;
   - extract one coherent calculation;
   - move a repeated rule to its owner.
5. Re-run the same checks. When using an analyzer, measure each original
   hotspot and every function created or extracted from it. Review the
   resulting callers as well as the scores.

## Do not game the metric

A refactor has not helped when it only:

- spreads branches across vaguely named helpers;
- replaces clear local logic with indirection;
- adds flags or configuration to conceal a decision;
- lowers per-function scores while making callers learn more;
- duplicates the same condition elsewhere;
- compresses branches into a clever expression.

Prefer deleting a decision, representing it more directly, or putting it in the
right place over merely relocating it.

## Report

Keep the result proportional. Report one row per refactored hotspot, not one row
per trivial helper. Use only metrics the selected analyzer actually provides.

Analyzer: project-selected analyzer, rule, and version

Metric: cyclomatic complexity, cognitive complexity, or another reported metric

| Hotspot        | Before | After | Change |
| -------------- | -----: | ----: | -----: |
| `processOrder` |     18 |     5 |   -72% |

Extracted functions, only when present:

| From hotspot   | Highest extracted function | Score |
| -------------- | -------------------------- | ----: |
| `processOrder` | `validateOrder`            |     6 |

Behavior verified: relevant checks and results

Calculate `Change` as `(after - before) / before * 100`, rounded to the nearest
whole percent. If the analyzer is unavailable, omit the numeric table and say
what qualitative evidence was used.
For newly written code, report the current score without inventing a baseline.
Do not aggregate scores across functions or describe the percentage as an
overall code-quality improvement. If the analyzer provides multiple relevant
metrics, identify the metric for each table instead of mixing unlike scores.

Keep tables narrow enough to scan in a terminal or on mobile. Omit the
extracted-functions table when nothing was extracted. If Markdown tables do not
render reliably, use one line per hotspot instead:

```text
processOrder: 18 -> 5 (-72%)
  highest extracted: validateOrder (6)
```

For each material hotspot, also state how behavior was verified. Do not flag
clear code solely because a metric is high.
