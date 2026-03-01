# Advanced Patterns

> These patterns are uncommon — most components only need a style protocol + configuration. Use these only when the specific problem demands it.

## State Machine Pattern

For the rare component with visual state transitions (e.g., idle → expanded → collapsed), separate into three concerns:

### 1. State Protocol — pure view factories

```swift
public protocol StepperState: Equatable {
    associatedtype Body: View

    var styleContext: StepperStyleContext { get }

    @ViewBuilder @MainActor
    func makeBody(
        style: any StepperStyle,
        configuration: StepperStyleConfiguration,
        onIncrement: @escaping () -> Void,
        onDecrement: @escaping () -> Void,
        onTap: @escaping () -> Void
    ) -> Body
}

public enum StepperStyleContext: Equatable, Sendable {
    case idle
    case expanded
    case collapsed
    case custom(String)
}
```

### 2. State Implementations — one struct per visual phase

```swift
public struct IdleState: StepperState {
    public var styleContext: StepperStyleContext { .idle }
    public init() {}

    public func makeBody(
        style: any StepperStyle,
        configuration: StepperStyleConfiguration,
        onIncrement: @escaping () -> Void,
        onDecrement: @escaping () -> Void,
        onTap: @escaping () -> Void
    ) -> some View {
        // Render idle UI (e.g., add button only)
    }
}
```

### 3. Behavior Protocol — pure transition logic

```swift
public protocol StepperBehavior: Sendable {
    func initialState(configuration: StepperStyleConfiguration) -> any StepperState
    func onIncrement(from state: any StepperState, configuration: StepperStyleConfiguration) -> any StepperState
    func onDecrement(from state: any StepperState, configuration: StepperStyleConfiguration) -> any StepperState
    func onTap(from state: any StepperState, configuration: StepperStyleConfiguration) -> any StepperState
}
```

**Key separation:** Behaviors are `Sendable` (pure logic, no environment). States are `Equatable` (for diffing). Styles are `DynamicProperty` (environment access).

### Behavior Implementation

```swift
public struct CollapsibleBehavior: StepperBehavior {
    public init() {}

    public func initialState(configuration: StepperStyleConfiguration) -> any StepperState {
        configuration.isAtMinimum ? IdleState() : CollapsedState()
    }

    public func onIncrement(from state: any StepperState, configuration: StepperStyleConfiguration) -> any StepperState {
        ExpandedState()
    }

    public func onDecrement(from state: any StepperState, configuration: StepperStyleConfiguration) -> any StepperState {
        configuration.isAtMinimum ? IdleState() : state
    }

    public func onTap(from state: any StepperState, configuration: StepperStyleConfiguration) -> any StepperState {
        state.styleContext == .expanded ? CollapsedState() : ExpandedState()
    }
}
```

### When to Use

- Component has 2+ distinct visual phases (idle, active, collapsed, expanded)
- Transition logic varies by context
- Need testable state transitions independent of UI

### File Organization

```
[Component]/
├── Behaviors/
│   ├── [Component]Behavior.swift
│   ├── Standard[Component]Behavior.swift
│   └── Collapsible[Component]Behavior.swift
├── States/
│   ├── [Component]State.swift
│   ├── Idle[Component]State.swift
│   ├── Expanded[Component]State.swift
│   └── Collapsed[Component]State.swift
├── EnvironmentKeys/
│   ├── [Component]StyleKey.swift
│   └── [Component]BehaviorKey.swift
├── Styles/
│   └── ...
└── [Component].swift
```

### Testing

For each behavior, test every transition:

```swift
@Suite("CollapsibleBehavior") @MainActor
struct CollapsibleBehaviorTests {

    let behavior = CollapsibleBehavior()

    @Test
    func initialState_idleAtMinimum() {
        let config = makeConfiguration(value: 0)
        let state = behavior.initialState(configuration: config)
        #expect(state.styleContext == .idle)
    }

    @Test
    func onIncrement_transitionsToExpanded() {
        let config = makeConfiguration(value: 1)
        let newState = behavior.onIncrement(from: IdleState(), configuration: config)
        #expect(newState.styleContext == .expanded)
    }

    @Test
    func onDecrement_toMinimum_transitionsToIdle() {
        let config = makeConfiguration(value: 0)
        let newState = behavior.onDecrement(from: ExpandedState(), configuration: config)
        #expect(newState.styleContext == .idle)
    }
}
```

Also test state equality and context:

```swift
@Suite("StepperState")
struct StepperStateTests {

    @Test
    func idleState_context() {
        #expect(IdleState().styleContext == .idle)
    }

    @Test
    func idleState_equality() {
        #expect(IdleState() == IdleState())
    }
}
```

---

## Dual Protocol Pattern (Style + Layout)

For components where visual decoration and spatial arrangement are orthogonal:

```swift
// Style — decorates individual items
public protocol TabViewStyle: DynamicProperty {
    associatedtype Body: View
    @ViewBuilder @MainActor
    func makeBody(configuration: TabViewStyleConfiguration) -> Body
}

// Layout — arranges items in container
public protocol TabViewLayout: DynamicProperty {
    associatedtype Body: View
    @ViewBuilder @MainActor
    func makeBody(
        tabs: [Tab],
        selection: Binding<AnyHashable>,
        content: @escaping @MainActor (Tab) -> AnyView
    ) -> Body
}
```

The component resolves both:

```swift
public var body: some View {
    resolveLayout(layout)
}

private func resolveLayout(_ layout: some TabViewLayout) -> AnyView {
    AnyView(
        layout.makeBody(tabs: resolvedTabs, selection: selectionBinding) { tab in
            resolveStyle(style, tab: tab)
        }
    )
}

private func resolveStyle(_ style: some TabViewStyle, tab: Tab) -> AnyView {
    AnyView(
        style.makeBody(configuration: .init(tab: tab, namespace: namespace))
    )
}
```

### When to Use

- Any style can combine with any layout (e.g., `underline` style + `scrollable` layout)
- Styles and layouts have independent environment keys
- Adding a new layout requires zero style changes and vice versa

---

## Result Builder Pattern

For components that accept a list of declarative child items:

```swift
@resultBuilder
public struct TabBuilder {
    public static func buildBlock(_ components: [Tab]...) -> [Tab] {
        components.flatMap { $0 }
    }

    public static func buildExpression(_ expression: Tab) -> [Tab] {
        [expression]
    }

    public static func buildOptional(_ component: [Tab]?) -> [Tab] {
        component ?? []
    }

    public static func buildEither(first: [Tab]) -> [Tab] { first }
    public static func buildEither(second: [Tab]) -> [Tab] { second }
}

// Usage
TabView(selection: $tab) {
    Tab("Home", value: .home)
    Tab("Search", value: .search)

    if isAdmin {
        Tab("Admin", value: .admin)
    }
}
```

### When to Use

- Component accepts N children of the same type
- Children need conditional logic (`if`/`else`)
- Declarative DSL feel is desired (like SwiftUI's own builders)
