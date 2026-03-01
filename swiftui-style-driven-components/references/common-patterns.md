# Common Patterns

## Convenience Initializers

Follow Apple's pattern for common content types:

```swift
extension Chip where Content == Text {
    
    /// Creates a chip with a localized string title.
    public init(
        _ titleKey: LocalizedStringKey,
        action: @escaping @MainActor () -> Void
    ) {
        self.init(action: action) {
            Text(titleKey)
        }
    }
    
    /// Creates a chip with a string title.
    public init<S>(
        _ title: S,
        action: @escaping @MainActor () -> Void
    ) where S: StringProtocol {
        self.init(action: action) {
            Text(title)
        }
    }
}
```

## Convenience Accessors

Provide static properties in constrained extensions:

```swift
extension CardStyle where Self == DefaultCardStyle {
    public static var automatic: DefaultCardStyle { .init() }
}

extension CardStyle where Self == CompactCardStyle {
    public static var compact: CompactCardStyle { .init() }
}
```

## Parameterized Styles

Styles can accept parameters:

```swift
public struct TintedCardStyle: CardStyle {

    public let tint: Color
    
    public init(tint: Color) {
        self.tint = tint
    }
    
    @MainActor
    public func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .padding()
            .background(tint.opacity(0.1))
            .clipShape(RoundedRectangle(cornerRadius: 8))
            .overlay(
                RoundedRectangle(cornerRadius: 8)
                    .stroke(tint, lineWidth: 1)
            )
    }
}

extension CardStyle where Self == TintedCardStyle {
    public static func tinted(_ color: Color) -> TintedCardStyle {
        TintedCardStyle(tint: color)
    }
}
```

## Environment-Reading Styles

Styles can read environment values (enabled by `DynamicProperty`):

```swift
public struct AdaptiveCardStyle: CardStyle {

    @Environment(\.colorScheme) private var colorScheme
    
    public init() {}
    
    @MainActor
    public func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .padding()
            .background(colorScheme == .dark ? Color.black : Color.white)
            .clipShape(RoundedRectangle(cornerRadius: 8))
    }
}
```

## Bindings in Configuration

For components that modify state (e.g., inputs, steppers), store the `Binding` directly:

```swift
public struct InputStyleConfiguration {
    
    @Binding public var text: String
    
    @MainActor init(text: Binding<String>) {
        _text = text
    }
}

// Style uses binding directly
public struct DefaultInputStyle: InputStyle {
    
    public init() {}
    
    @MainActor
    public func makeBody(configuration: Configuration) -> some View {
        TextField("", text: configuration.$text)
            .background(Color.white)
            .foregroundStyle(.black)
            .font(.body)
    }
}
```

## Roles and Behaviors

Use semantic roles for automatic configuration:

```swift
public enum InputRole {
    case input
    case emailAddress
    case password
    case phoneNumber
    case otp
    
    var keyboardType: UIKeyboardType {
        switch self {
        case .emailAddress: .emailAddress
        case .phoneNumber: .numberPad
        case .otp: .numberPad
        default: .default
        }
    }
    
    var textContentType: UITextContentType? {
        switch self {
        case .emailAddress: .emailAddress
        case .password: .password
        case .phoneNumber: .telephoneNumber
        case .otp: .oneTimeCode
        default: nil
        }
    }
}
```
