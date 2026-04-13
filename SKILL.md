---
name: jetpack-compose-audit
description: Audit Android Jetpack Compose repositories for performance, state management, side effects, and composable API quality. Scans source code, scores each category from 0-10, writes a strict markdown report, and summarizes the most important fixes. Use when reviewing a Compose codebase, rating repository quality, inspecting recomposition/state issues, or running a Compose audit.
user-invokable: true
argument-hint: "[repo path or module path]"
---

# Jetpack Compose Audit

This skill audits Android Jetpack Compose repositories with a strict, evidence-based report.

It is intentionally focused on four categories:

- Performance
- State management
- Side effects
- Composable API quality

This skill does **not** score design or Material 3 compliance in v1. If the audit surfaces likely design-system problems, recommend a follow-up audit with [`material-3`](https://github.com/hamen/material-3-skill).

## When To Use

Use this skill when the user asks to:

- audit a Jetpack Compose repository
- review Compose architecture or quality
- rate a codebase with scores
- inspect recomposition, state, or effects issues
- identify Compose best-practice violations in an existing repo

Typical trigger phrases:

- "audit this Compose repo"
- "score this Jetpack Compose codebase"
- "review state hoisting and side effects"
- "check Compose performance"
- "rate this repository"

## Expected Output

Produce both:

- a repository report file named `COMPOSE-AUDIT-REPORT.md`
- a short chat summary with the overall score, category scores, worst issues, and the top fixes

## Audit Principles

- Be strict, but evidence-based.
- Do not score from search hits alone. Read representative files before judging a category.
- Cite concrete file paths in the report for every important deduction.
- Prefer canonical Android guidance over folklore.
- Treat performance as important, but not as the only lens.
- Do not punish app code for failing public-library purity tests. Apply API-quality checks mainly to reusable internal components, design-system pieces, and shared UI building blocks.
- Reserve `0-3` scores for repeated or systemic problems, not isolated mistakes.
- Do not award `9-10` unless the repo is consistently strong across the category.

## Process

### 1. Confirm Scope

Identify the target path. If the user gave a repository root, audit the relevant Compose modules inside it.

If the codebase is not an Android Jetpack Compose repository, stop and report that it is out of scope.

### 2. Map The Repository

Before scoring, identify:

- Gradle modules
- Android app and feature modules
- likely Compose source roots
- shared UI/component packages
- theme or design-system packages
- state holder or ViewModel areas
- test and preview locations
- baseline-profile related modules or config if present

### 3. Build A Compose Surface Map

Look for:

- `@Composable` functions
- reusable UI components
- screens and routes
- `ViewModel` usage
- `remember`, `rememberSaveable`, `mutableStateOf`
- `collectAsStateWithLifecycle`, `collectAsState`
- `LaunchedEffect`, `DisposableEffect`, `SideEffect`, `rememberUpdatedState`, `produceState`
- `LazyColumn`, `LazyRow`, `items`, `itemsIndexed`

If the repo is large, audit by category or by module. If subagents are available, parallelize category scans in readonly mode and merge the findings.

### 4. Audit The Four Categories

Use the scoring rubric in `references/scoring.md` and the heuristics in `references/search-playbook.md`.

#### Performance

Focus on:

- expensive work in composition
- avoidable recomposition
- lazy list keys
- bad state-read timing
- unstable or overly broad reads
- backwards writes
- obvious release-performance hygiene where visible

#### State Management

Focus on:

- hoisting correctness
- single source of truth
- reusable stateless seams
- correct use of `remember` vs `rememberSaveable`
- lifecycle-aware observable collection
- observable vs non-observable mutable state

#### Side Effects

Focus on:

- side effects incorrectly done in composition
- correct effect API choice
- effect keys
- stale lambda capture
- cleanup correctness
- lifecycle-aware effect behavior

#### Composable API Quality

Focus on reusable internal components, not every leaf screen.

Check:

- `modifier` presence and placement
- parameter order
- explicit over implicit configuration
- meaningful defaults
- avoiding `MutableState<T>` or `State<T>` parameters in reusable APIs where a better shape exists
- component purpose and layering

### 5. Verify Findings

Before deducting points:

- read the file where the smell appears
- make sure the pattern is real, not a false positive
- check whether the repo already has a compensating pattern elsewhere
- distinguish one-off mistakes from systemic patterns

### 6. Score

Assign each category a `0-10` score and a status:

- `0-3`: fail
- `4-6`: needs work
- `7-8`: solid
- `9-10`: excellent

Use the weights in `references/scoring.md` to compute the overall score.

If a category genuinely has too little auditable surface area, mark it `N/A`, explain why, and renormalize the remaining weights.

### 7. Write The Report

Use `references/report-template.md`.

The report must include:

- overall score
- category score table
- top critical findings
- category-by-category reasoning
- evidence file paths
- prioritized remediation list
- optional follow-up note to run `material-3` if design issues are suspected

Write the report to:

- `COMPOSE-AUDIT-REPORT.md`

### 8. Return A Short Summary

In chat, summarize:

- overall score
- one-line judgment for each category
- the three most important fixes
- whether a `material-3` audit is worth running next

## Evidence Rules

- Prefer multiple examples over one dramatic example.
- Use positive evidence too, not just failures.
- Do not infer runtime problems you cannot justify from the code.
- When a rule is based on official guidance but app-level tradeoffs may justify deviation, call it out as a tradeoff instead of pretending it is always wrong.

## Large Repo Strategy

For medium or large repositories:

1. Map modules first.
2. Pick representative files per module.
3. Parallelize category scans when possible.
4. Merge repeated findings into systemic issues instead of listing the same smell twenty times.

## What To Avoid

- Do not produce a generic checklist with no repository evidence.
- Do not turn the report into a public-library API lecture if the repo is an app.
- Do not inflate the performance score just because the app uses Compose.
- Do not over-penalize isolated experiments or sample files unless they are part of production paths.
- Do not score design in v1.

## References

- `references/scoring.md`
- `references/search-playbook.md`
- `references/report-template.md`
- `references/canonical-sources.md`
