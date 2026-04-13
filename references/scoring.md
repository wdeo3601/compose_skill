# Scoring

## Category Weights

Use these default weights for Android Jetpack Compose app repositories:

| Category | Weight |
|----------|--------|
| Performance | 35% |
| State management | 25% |
| Side effects | 20% |
| Composable API quality | 20% |

If a category is truly `N/A`, remove it and renormalize the remaining weights.

## Score Bands

| Score | Status | Meaning |
|-------|--------|---------|
| 0-3 | fail | Systemic issues, repeated misuse, or architecture-level risk |
| 4-6 | needs work | Mixed quality, recurring smells, meaningful refactor value |
| 7-8 | solid | Mostly healthy with some targeted fixes needed |
| 9-10 | excellent | Consistently strong patterns, only minor issues |

## Overall Score

Report both:

- per-category scores on a `0-10` scale
- an overall score on a `0-100` scale

Compute:

`overall = weighted_average(category_scores) * 10`

Round to the nearest whole number.

## Confidence

Add a confidence note in the report:

- `High`: enough Compose surface area, multiple representative modules/files read
- `Medium`: some categories based on limited sample size
- `Low`: small repo, partial module access, or sparse Compose surface

Low confidence does not block scoring, but it must be stated clearly.

## Category Rubric

### Performance

Reward:

- expensive calculations cached with `remember` or moved out of composition
- stable keys in lazy layouts where list identity matters
- good use of `derivedStateOf` for fast-changing state
- deferred reads and lambda modifiers for frequently changing values
- absence of obvious backwards writes
- evidence of performance-aware configuration such as baseline profiles or release-minded setup when relevant

Deduct for:

- collection transforms or expensive computation inside composable bodies without caching
- lazy list items without stable keys when identity/moves matter
- reading rapidly changing state too high in the tree
- frequent-state modifiers that force composition when layout or draw would suffice
- backwards writes or other patterns that can trigger runaway recomposition
- repeated broad recomposition smells across screens/components

Suggested interpretation:

- `9-10`: clean patterns are common and performance smells are rare
- `7-8`: minor or localized issues
- `4-6`: repeated recomposition or lazy-list issues
- `0-3`: serious, widespread performance problems or unsafe state-write patterns

### State Management

Reward:

- clear single source of truth
- correct hoisting to the lowest common reader / highest writer
- related state hoisted together when driven by the same events
- stateless reusable composables with stateful wrappers where useful
- `rememberSaveable` for UI state that should survive recreation
- `collectAsStateWithLifecycle()` in Android UI code
- observable immutable state instead of mutable non-observable containers
- use of plain state-holder classes when screen logic grows

Deduct for:

- duplicated or split ownership of state
- under-hoisted shared state
- reusable components with unnecessary internal state
- misuse of `remember` where `rememberSaveable` is more appropriate
- non-observable mutable collections or mutable data holders used as state
- Android flows collected in UI without lifecycle awareness when the code is Android-specific
- state scattered across multiple sibling composables without a clear owner

Suggested interpretation:

- `9-10`: strong UDF, clear ownership, minimal ambiguity
- `7-8`: mostly healthy with some hoisting or saveability gaps
- `4-6`: repeated ownership confusion or weak state boundaries
- `0-3`: systemic duplication, stale data risks, or non-observable state misuse

### Side Effects

Reward:

- side-effect-free composition
- correct use of `LaunchedEffect`, `DisposableEffect`, `SideEffect`, `rememberUpdatedState`, and `produceState`
- effects keyed to the right lifecycle inputs
- cleanup for listeners, observers, and subscriptions
- stale callback capture avoided with `rememberUpdatedState` when needed

Deduct for:

- launching threads, coroutines, navigation, or external work directly in composition
- wrong effect API choice
- incorrect or missing effect keys
- stale captures in long-lived effects
- empty or suspicious `onDispose`
- listeners or observers registered without cleanup

Suggested interpretation:

- `9-10`: deliberate, lifecycle-aware effect usage
- `7-8`: mostly correct with small effect-key or cleanup issues
- `4-6`: recurring misuse of effects or composition-time work
- `0-3`: side effects commonly happen in composition or cleanup is broadly unsafe

### Composable API Quality

This category is lighter-touch for app repositories. Focus on shared internal components, UI kits, and reusable building blocks.

Reward:

- reusable components expose `modifier: Modifier = Modifier`
- `modifier` is the first optional parameter and applied once to the root-most UI node
- parameter order is sensible: required, `modifier`, optional, trailing content lambda
- defaults are meaningful and not hidden behind nullable sentinel behavior
- explicit parameters are preferred over component-specific `CompositionLocal` indirection
- components are focused and layered instead of multipurpose grab-bags
- reusable APIs avoid `MutableState<T>` params when a stateless or state-holder API would be better

Deduct for:

- shared components missing `modifier`
- multiple modifier params or modifier applied to the wrong child
- `modifier` with a non-no-op default like padding
- parameter ordering that makes APIs awkward or misleading
- nullable params used only as "use internal default" signals
- `MutableState<T>` or `State<T>` params in reusable APIs when avoidable
- giant multipurpose components that should be split or wrapped

Suggested interpretation:

- `9-10`: shared UI APIs are clean, predictable, and reusable
- `7-8`: solid conventions with a few inconsistencies
- `4-6`: repeated API smell in shared components
- `0-3`: shared component APIs are confusing, rigid, or actively error-prone

## Scoring Rules

- One isolated issue should not tank a whole category.
- Repeated issues across several modules should count more than a single bad file.
- Production code matters more than samples, previews, or scratch files.
- Positive patterns should raise confidence and may offset minor issues.
- App teams may intentionally deviate from framework/library guidance. Note the tradeoff before deducting heavily.
