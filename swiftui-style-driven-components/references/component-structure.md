# Component Structure

## File Organization

Every style-driven component follows this structure:

```
Sources/[Library]/Components/[Component]/
├── EnvironmentKeys/
│   └── [Component]StyleKey.swift
├── Styles/
│   ├── [Component]Style.swift
│   ├── [Component]StyleConfiguration.swift
│   └── Default[Component]Style.swift
└── [Component].swift

Sources/[Library]/Support/Previews/
└── [Component]Previews.swift

Tests/[Library]Tests/Components/
└── [Component]Tests.swift
```

## Optional Folders

### Content Folder

Add `Content/[Component]Content.swift` **only** when:
- Multiple styles share identical layout
- Styles differ only in visual properties (colors, spacing)
- You want to avoid duplicating layout code

Skip when:
- Each style has unique layout
- Component is simple
- Styles need full control over view hierarchy

## Naming Conventions

- **Component**: `[Component]`
- **Style Protocol**: `[Component]Style`
- **Style Implementation**: `Default[Component]Style`, `Compact[Component]Style`
- **Configuration**: `[Component]StyleConfiguration`
- **Environment Key File**: `[Component]StyleKey.swift`
- **Preview File**: `[Component]Previews.swift`
- **Test File**: `[Component]Tests.swift`

## MARK Comment Convention

Use consistent MARK sections across all component files:

```swift
public struct Card<Content>: View where Content: View {
    
    // MARK: - Properties
    
    private let action: () -> Void
    private let content: Content
    
    // MARK: - Environment
    
    @Environment(\.cardStyle) private var style
    
    // MARK: - State (if needed)
    
    @State private var isPressed = false
    
    // MARK: - Initializer
    
    public init(
        action: @escaping @MainActor () -> Void,
        @ViewBuilder content: () -> Content
    ) {
        self.action = action
        self.content = content()
    }

    // MARK: - Body

    public var body: some View {
        AnyView(
            style.makeBody(
                configuration: .init(content: content)
            )
        )
    }
}

// MARK: - Convenience Extensions

extension Card where Content == Text {
    // ...
}
```

For configuration files:

```swift
public struct CardStyleConfiguration {

    // MARK: - Type-Erased Views
    
    public struct Content: View { ... }
    
    // MARK: - Properties
    
    public let content: Content
    
    // MARK: - Initializer
    
    @MainActor
    init<Content: View>(content: Content) { ... }
}
```

## Component Implementation

```swift
public struct Card<Content>: View where Content: View {
    
    // MARK: - Properties

    private let action: () -> Void
    private let content: Content
    
    // MARK: - Environment

    @Environment(\.cardStyle) private var style

    // MARK: - Initializer

    public init(
        action: @escaping @MainActor () -> Void,
        @ViewBuilder content: () -> Content
    ) {
        self.action = action
        self.content = content()
    }

    // MARK: - Body

    public var body: some View {
        AnyView(
            style.makeBody(
                configuration: .init(content: content)
            )
        )
    }
}
```

## Key Principles

1. **Defining Parameters**: Initializer takes only what defines the component
2. **Internal Configuration**: Configuration created in body, not passed to initializer
3. **Generic Content**: Use generics for flexible content types
4. **ViewBuilder**: Use `@ViewBuilder` for content closures
5. **Environment**: Read style from environment using `any [Component]Style`
6. **AnyView Wrapper**: Wrap `style.makeBody` in `AnyView` for type erasure
