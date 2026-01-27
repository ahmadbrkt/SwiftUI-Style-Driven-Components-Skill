# Accessibility

## Requirements

Style-driven components must be accessible:

- Semantic labels (`accessibilityLabel`, `accessibilityHint`) — **must be localized**
- VoiceOver navigation
- Dynamic Type support
- `accessibilityIdentifier` for UI testing — **must use constants**

## Basic Implementation

```swift
Card("Action", action: { })
    .accessibilityLabel(Text("card_action_label", bundle: .module))
    .accessibilityHint(Text("card_action_hint", bundle: .module))
    .accessibilityIdentifier(AccessibilityIdentifier.Card.action)
```

## Localized Labels

Always use localized strings for semantic labels:

```swift
// Localizable.strings
"card_action_label" = "Action card";
"card_action_hint" = "Double tap to perform action";

// Usage
.accessibilityLabel(Text("card_action_label", bundle: .module))
.accessibilityHint(Text("card_action_hint", bundle: .module))
```

Or with String Catalogs:

```swift
.accessibilityLabel(Text("Action card", bundle: .module))
.accessibilityHint(Text("Double tap to perform action", bundle: .module))
```

## Accessibility Identifiers

Use constants, never hardcoded strings:

```swift
// AccessibilityIdentifier.swift
public enum AccessibilityIdentifier {
    public enum Card {
        public static let action = "card.action"
        public static let settings = "card.settings"
        public static let profile = "card.profile"
    }
    
    public enum Button {
        public static let primary = "button.primary"
        public static let secondary = "button.secondary"
    }
}

// Usage
Card("Action", action: { })
    .accessibilityIdentifier(AccessibilityIdentifier.Card.action)
```

## In Styles

Styles should preserve accessibility:

```swift
struct DefaultCardStyle: CardStyle {
    @MainActor
    func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .padding()
            .background(Color.secondary.opacity(0.1))
            .clipShape(RoundedRectangle(cornerRadius: 8))
            .contentShape(Rectangle())  // Full tap area
    }
}
```

## Dynamic Type

Adapt to Dynamic Type sizes:

```swift
struct DefaultCardStyle: CardStyle {
    @Environment(\.dynamicTypeSize) private var dynamicTypeSize
    
    @MainActor
    func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .padding(padding(for: dynamicTypeSize))
    }
    
    private func padding(for size: DynamicTypeSize) -> CGFloat {
        switch size {
        case .accessibility3, .accessibility4, .accessibility5:
            return 24
        case .xxxLarge, .accessibility1, .accessibility2:
            return 20
        default:
            return 16
        }
    }
}
```

## Hide Decorative Elements

```swift
Rectangle()
    .fill(Color.gray.opacity(0.3))
    .frame(height: 1)
    .accessibilityHidden(true)
```

## Group Related Elements

```swift
VStack {
    configuration.content
}
.accessibilityElement(children: .combine)
```

## Checklist

- [ ] `accessibilityLabel` uses localized strings
- [ ] `accessibilityHint` uses localized strings
- [ ] `accessibilityIdentifier` uses constants (not hardcoded)
- [ ] Dynamic Type supported
- [ ] VoiceOver works correctly
- [ ] Decorative elements hidden
- [ ] Minimum 44x44pt tap targets
