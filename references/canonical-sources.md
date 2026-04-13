# Canonical Sources

Use these as the source of truth for v1 scoring and guidance.

## Primary Sources

### Performance

- Android Developers: `Follow best practices`  
  `https://developer.android.com/develop/ui/compose/performance/bestpractices`
- Android Developers: `Jetpack Compose Performance`  
  `https://developer.android.com/develop/ui/compose/performance`

These ground:

- `remember` for expensive work
- lazy list keys
- `derivedStateOf`
- deferred state reads
- lambda modifiers
- backwards writes
- performance mindset and baseline-profile awareness

### State

- Android Developers: `State and Jetpack Compose`  
  `https://developer.android.com/develop/ui/compose/state`

This grounds:

- state hoisting rules
- stateful vs stateless composables
- `remember` vs `rememberSaveable`
- observable vs non-observable mutable state
- lifecycle-aware collection of observable state
- plain state-holder classes

### Side Effects

- Android Developers: `Side-effects in Compose`  
  `https://developer.android.com/develop/ui/compose/side-effects`

This grounds:

- side-effect-free composition
- `LaunchedEffect`
- `DisposableEffect`
- `SideEffect`
- `rememberUpdatedState`
- `produceState`
- lifecycle-aware effect behavior

### Composable API Quality

- Android Developers: `Style guidelines for Jetpack Compose APIs`  
  `https://developer.android.com/develop/ui/compose/api-guidelines`
- AndroidX component guidelines: `API Guidelines for @Composable components in Jetpack Compose`  
  `https://android.googlesource.com/platform/frameworks/support/+/androidx-main/compose/docs/compose-component-api-guidelines.md`

These ground:

- `modifier` conventions
- parameter order
- explicit vs implicit dependencies
- meaningful defaults
- component layering
- avoiding `MutableState<T>` and `State<T>` in reusable APIs when better shapes exist

## Supplemental Sources

These are useful for extra examples and ecosystem framing, but they do **not** override the primary sources:

- `https://github.com/skydoves/compose-performance`
- `https://medium.com/@idaoskooei/building-better-uis-with-jetpack-compose-best-practices-and-techniques-a1c8953bc5b8`

## Adjacent Skill Pattern

This skill intentionally pairs well with:

- `https://github.com/hamen/material-3-skill`

That skill covers Material 3 design and design-system audit concerns. This Compose audit skill should mention it as a follow-up when visual or design-system problems are suspected, but should not score design in v1.
