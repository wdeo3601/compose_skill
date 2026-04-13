# Search Playbook

Use this playbook to gather evidence quickly, then verify by reading representative files.

## 1. Map The Repo First

Before category scoring, locate:

- Gradle settings and module declarations
- Android app and feature modules
- likely Compose source folders
- shared component or design-system directories
- theme packages
- screen packages
- ViewModels and state holders
- test and preview directories

Useful targets often include:

- `app/`
- `feature*/`
- `core/ui/`
- `designsystem/`
- `ui/components/`
- `ui/screens/`
- `presentation/`

## 2. Confirm Compose Surface Area

Search for:

- `@Composable`
- `MaterialTheme`
- `setContent`
- `ComposeView`
- `remember`
- `mutableStateOf`

If Compose usage is sparse, state that clearly in the report and reduce confidence.

## 3. Performance Checks

### Search For

- `remember\(`
- `derivedStateOf`
- `LazyColumn|LazyRow|LazyVerticalGrid|LazyHorizontalGrid`
- `items\(`
- `itemsIndexed\(`
- `rememberLazyListState`
- `animate`
- `offset\(`
- `drawBehind`
- `sortedWith|sortedBy|filter|map|associate|groupBy`

### Red Flags To Verify

- expensive list transforms inside composable bodies
- expensive work passed directly to `items(...)`
- `Lazy*` items without `key =` where item identity can move or reorder
- scroll or animation state read high in the tree
- fast-changing values passed to non-lambda modifiers when a layout/draw-phase alternative exists
- suspicious writes to state after reads in the same composable

### Positive Signals

- `remember(keys)` around expensive calculations
- `derivedStateOf` used for scroll-triggered UI thresholds
- lambda modifiers such as `offset { ... }`
- draw/layout phase reads instead of full recomposition for rapidly changing values
- baseline-profile modules or profile installer setup when app maturity suggests it matters

## 4. State Management Checks

### Search For

- `mutableStateOf`
- `rememberSaveable`
- `collectAsStateWithLifecycle`
- `collectAsState`
- `observeAsState`
- `subscribeAsState`
- `MutableState<`
- `State<`
- `mutableListOf|mutableMapOf|mutableSetOf|ArrayList`
- `CompositionLocal`
- `ViewModel`

### Red Flags To Verify

- duplicated state across parent/child or sibling composables
- shared state held too low in the tree
- reusable components with unnecessary internal state
- Android UI code using `collectAsState()` where `collectAsStateWithLifecycle()` is a better fit
- non-observable mutable collections used as state
- `MutableState<T>` params in reusable components
- `State<T>` params where a value or lambda would be more flexible

### Positive Signals

- clear stateful wrapper + stateless reusable composable split
- state hoisted to the lowest common reader / highest writer
- related state hoisted together
- lifecycle-aware collection of flows in Android UI
- plain state-holder classes for larger screens or app shells

## 5. Side Effects Checks

### Search For

- `LaunchedEffect`
- `DisposableEffect`
- `SideEffect`
- `rememberUpdatedState`
- `produceState`
- `snapshotFlow`
- `rememberCoroutineScope`
- `launch \(?`
- `Thread\(`
- `GlobalScope`
- `LifecycleEventObserver`

### Red Flags To Verify

- work started directly in composition body
- navigation, snackbar, analytics, or repository calls triggered during composition instead of from an effect or event path
- `LaunchedEffect(Unit)` or `LaunchedEffect(true)` with stale callback capture
- `DisposableEffect` with empty or suspicious cleanup
- listener/observer registration without `onDispose`
- effect keys too broad or too narrow

### Positive Signals

- `rememberUpdatedState` used for long-lived effects that should keep latest callbacks
- `DisposableEffect` paired with clear cleanup
- `SideEffect` used only to publish state to non-Compose code after successful composition
- `produceState` or equivalent used for converting external async sources into Compose state

## 6. Composable API Quality Checks

Focus on shared components and internal UI kit code, not every screen.

### Search For

- shared component directories such as `components`, `commonui`, `designsystem`, `ui/components`
- function signatures around `@Composable`
- `modifier: Modifier =`
- `Modifier = Modifier.`
- `MutableState<`
- `State<`
- `CompositionLocal`

### Red Flags To Verify

- shared components missing a `modifier`
- `modifier` not being the first optional parameter
- `modifier` default not equal to `Modifier`
- multiple modifier parameters on one component
- modifier applied to a child instead of the root-most emitted UI
- nullable params used to mean "choose internal default"
- component-specific configuration hidden behind implicit locals
- huge multipurpose components that should be layered or split

### Positive Signals

- required params first, then `modifier`, then optional params, then trailing content lambda
- explicit config exposed as parameters with meaningful defaults
- focused component responsibilities
- internal wrappers built on simpler lower-level components

## 7. Read Representative Files

For each category, read enough code to cover:

- at least one screen
- at least one shared component area
- at least one state-owning area such as a ViewModel or state-holder
- any suspicious files surfaced by search

Do not rely on one "bad" file to characterize the entire repo.

## 8. Merge Repeated Findings

Prefer:

- "7 shared components miss `modifier`"

over:

- seven separate bullets saying the same thing

Still keep 2-4 concrete file examples to justify the systemic finding.

## 9. Out-Of-Scope Cases

Stop or narrow scope if:

- the repo is not Android Jetpack Compose
- Compose is only present in demo or sample code
- the user asked to audit only one module or feature

In those cases, explain the limitation and score only the relevant surface area.
