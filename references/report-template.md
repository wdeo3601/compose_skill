# Report Template

Write the audit report to `COMPOSE-AUDIT-REPORT.md` using this structure.

```markdown
# Jetpack Compose Audit Report

Target: [repo path or module path]
Date: [date]
Scope: [modules or directories audited]
Confidence: [High | Medium | Low]
Overall Score: [X/100]

## Scorecard

| Category | Score | Weight | Status | Notes |
|----------|-------|--------|--------|-------|
| Performance | X/10 | 35% | [fail / needs work / solid / excellent] | [short note] |
| State management | X/10 | 25% | [fail / needs work / solid / excellent] | [short note] |
| Side effects | X/10 | 20% | [fail / needs work / solid / excellent] | [short note] |
| Composable API quality | X/10 | 20% | [fail / needs work / solid / excellent] | [short note] |

## Critical Findings

List the most important findings first. Each finding should include:

- severity
- why it matters
- 2-4 concrete file examples
- the likely fix direction

Example format:

1. **Performance: repeated expensive work happens inside composition**
   - Why it matters: [brief reason]
   - Evidence: `path/a.kt`, `path/b.kt`
   - Fix direction: [brief recommendation]

## Category Details

### Performance — [X/10]

**What is working**

- [positive evidence]

**What is hurting the score**

- [problem 1]
- [problem 2]

**Evidence**

- `path/to/file1.kt` — [brief reason]
- `path/to/file2.kt` — [brief reason]

### State Management — [X/10]

**What is working**

- [positive evidence]

**What is hurting the score**

- [problem 1]
- [problem 2]

**Evidence**

- `path/to/file1.kt` — [brief reason]
- `path/to/file2.kt` — [brief reason]

### Side Effects — [X/10]

**What is working**

- [positive evidence]

**What is hurting the score**

- [problem 1]
- [problem 2]

**Evidence**

- `path/to/file1.kt` — [brief reason]
- `path/to/file2.kt` — [brief reason]

### Composable API Quality — [X/10]

**What is working**

- [positive evidence]

**What is hurting the score**

- [problem 1]
- [problem 2]

**Evidence**

- `path/to/file1.kt` — [brief reason]
- `path/to/file2.kt` — [brief reason]

## Prioritized Fixes

1. [Highest leverage fix]
2. [Second fix]
3. [Third fix]
4. [Optional follow-up]

## Notes And Limits

- [state if only part of the repo was audited]
- [state if confidence is medium/low]
- [state if some categories had limited surface area]

## Suggested Follow-Up

- Run `material-3` audit if the repo also shows likely design-system or Material 3 problems.
```

## Tone

- Keep the tone strict and direct.
- Avoid filler praise.
- Give credit only where the codebase actually demonstrates good patterns.
- Prefer a few strong findings over dozens of weak bullets.
