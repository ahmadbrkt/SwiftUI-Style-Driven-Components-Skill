# Environment Keys

## Implementation Pattern

Environment keys enable subtree styling without modifying individual components:

```swift
extension EnvironmentValues {
    @Entry public var cardStyle: any CardStyle = .automatic
}

extension View {
    /// Sets the style for `Card` views within this view hierarchy.
    public func cardStyle(_ style: some CardStyle) -> some View {
        environment(\.cardStyle, style)
    }
}
```

## Usage Patterns

### Single Component
```swift
Card("Title", action: { })
    .cardStyle(.compact)
```

### Subtree Styling
```swift
VStack {
    Card("First", action: { })
    Card("Second", action: { })
    Card("Third", action: { })
}
.cardStyle(.compact)  // Applies to all
```

### Override in Subtree
```swift
VStack {
    Card("Compact", action: { })
    
    Card("Default", action: { })
        .cardStyle(.automatic)  // Overrides parent
}
.cardStyle(.compact)
```

### Default Behavior
```swift
Card("Title", action: { })  // Uses .automatic from environment
```

## Component Implementation

Read style from environment, wrap in `AnyView`:

```swift
public struct Card<Content: View>: View {

    @Environment(\.cardStyle) private var style
    
    public var body: some View {
        AnyView(
            style.makeBody(
                configuration: .init(content: content)
            )
        )
    }
}
```

## Why `some` in Modifier?

```swift
// Preferred — cleaner, modern Swift
public func cardStyle(_ style: some CardStyle) -> some View

// Equivalent — more verbose
public func cardStyle<S: CardStyle>(_ style: S) -> some View
```

Use `<S: CardStyle>` only when:
- Need to reference `S` multiple times
- Need `where` clauses
- Targeting Swift < 5.7

## Best Practices

- Use `@Entry` macro (Swift 5.9+)
- Store as existential type (`any CardStyle`)
- Provide concrete default (`.automatic`)
- Use opaque parameter (`some CardStyle`) in modifier
- Document usage examples
