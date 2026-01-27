# Style Protocol Pattern

## Protocol Definition

Every style-driven component uses a style protocol pattern:

```swift
/// A type that defines the visual appearance of a styled card.
public protocol CardStyle: DynamicProperty {
    associatedtype Body: View
    
    typealias Configuration = CardStyleConfiguration
    
    @ViewBuilder @MainActor 
    func makeBody(configuration: Configuration) -> Body
}
```

### Key Characteristics

1. **DynamicProperty**: Allows styles to use `@Environment`, `@State`
2. **Configuration**: Uses `typealias Configuration` for the configuration type
3. **ViewBuilder**: Uses `@ViewBuilder` for flexible view composition
4. **MainActor**: Uses `@MainActor` for UI thread safety

### Why DynamicProperty?

Styles can read environment values:

```swift
public struct ThemedCardStyle: CardStyle {
    @Environment(\.colorScheme) private var colorScheme
    
    @MainActor
    public func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .background(colorScheme == .dark ? Color.black : Color.white)
    }
}
```

## Configuration Pattern

Use nested type-erased views for content properties:

```swift
public struct CardStyleConfiguration {

    /// A type-erased content view.
    public struct Content: View {
        private let _body: () -> AnyView

        public init<Body: View>(_ body: Body) {
            _body = { AnyView(body) }
        }

        public var body: some View { _body() }
    }

    /// The main content of the card.
    public let content: Content

    /// Internal — only `Card` creates configurations.
    @MainActor
    init<Content: View>(content: Content) {
        self.content = .init(content)
    }
}
```

### Why Internal Init?

- Configurations are implementation details
- Only the component knows how to create valid configurations
- Prevents misuse by external code

### Why Nested Type-Erased Views?

- Follows Apple's pattern (`LabelStyleConfiguration.Title`)
- Each nested struct conforms to `View`
- Generic init maintains type safety at creation
- Clean API for style implementations

## Default Style Implementation

```swift
public struct DefaultCardStyle: CardStyle {

    public init() {}
    
    @MainActor
    public func makeBody(configuration: Configuration) -> some View {
        configuration.content
    }
}

extension CardStyle where Self == DefaultCardStyle {
    public static var automatic: DefaultCardStyle { .init() }
}
```

## Adding New Styles

Create new style without modifying component:

```swift
public struct CompactCardStyle: CardStyle {
    
    public init() {}
        
    @MainActor
    public func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .padding()
    }
}

extension CardStyle where Self == CompactCardStyle {
    public static var compact: CompactCardStyle { .init() }
}
```

**Usage:**
```swift
Card("Title", action: { })
    .cardStyle(.compact)
```

## Multiple Content Properties

For components with header, content, footer:

```swift
public struct CardStyleConfiguration {

    public struct Header: View {
        private let _body: () -> AnyView
        public init<Body: View>(_ body: Body) { 
            _body = { AnyView(body) } 
        }
        public var body: some View { _body() }
    }

    public struct Content: View {
        private let _body: () -> AnyView
        public init<Body: View>(_ body: Body) { 
            _body = { AnyView(body) } 
        }
        public var body: some View { _body() }
    }

    public struct Footer: View {
        private let _body: () -> AnyView
        public init<Body: View>(_ body: Body) { 
            _body = { AnyView(body) } 
        }
        public var body: some View { _body() }
    }

    public let header: Header
    public let content: Content
    public let footer: Footer

    @MainActor 
    init<Header, Content, Footer>(
        header: Header,
        content: Content,
        footer: Footer
    ) where Header: View, Content: View, Footer: View {
        self.header = .init(header)
        self.content = .init(content)
        self.footer = .init(footer)
    }
}
```

## Anti-Patterns

### **DON'T** use public configuration initializer
```swift
// WRONG
public init(content: C) { ... }
```

### **DON'T** use `AnyView` directly in configuration
```swift
// WRONG
public struct CardStyleConfiguration {
    public let content: AnyView
}
```

### **DON'T** put content properties in style protocol
```swift
// WRONG
protocol CardStyle {
    var content: AnyView { get }
}
```

### **DO** use nested type-erased views with internal init
```swift
// CORRECT
public struct CardStyleConfiguration {
    public struct Content: View {
        private let _body: () -> AnyView
        public init<Body: View>(_ body: Body) { 
            _body = { AnyView(body) } 
        }
        public var body: some View { _body() }
    }
    
    public let content: Content
    
    @MainActor
    init<Content: View>(content: Content) { 
        self.content = .init(content) 
    }
}
```
