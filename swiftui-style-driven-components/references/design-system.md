# Design System Integration

## Overview

Style implementations should use design tokens, not hardcoded values.

## Token Categories

### Design Tokens
- **Spacing** — xxSmall, xSmall, small, medium, large, xLarge, xxLarge
- **Corner Radius** — none, small, medium, large, xLarge, full
- **Border Width** — thin, medium, thick
- **Sizes** — ButtonSize, IconSize, AvatarSize, etc.

### Typography
- Display (large, medium, small)
- Headline (large, medium, small)
- Title (large, medium, small)
- Body (large, medium, small)
- Label (large, medium, small)

### Colors
- **Text** — primary, secondary, tertiary, disabled, inverse
- **Background** — primary, secondary, tertiary
- **Border** — primary, secondary
- **Brand** — primary, secondary
- **Status** — success, warning, error, info

### Gradients
- Linear gradients
- Angular gradients
- Radial gradients

### Symbol Assets
- **Icons** — enum with SF Symbols or custom assets
- **Illustrations** — enum with bundle assets

## Usage in Styles

```swift
struct DefaultCardStyle: CardStyle {
    @MainActor
    func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .padding(Spacing.medium)
            .background(Color.Background.secondary)
            .clipShape(RoundedRectangle(cornerRadius: CornerRadius.medium))
    }
}
```

## Anti-Patterns
### **DON'T** use magic numbers
```swift
// WRONG
.padding(16)
.cornerRadius(8)
```

### **DO** use design tokens
```swift
// CORRECT
.padding(Spacing.medium)
.clipShape(RoundedRectangle(cornerRadius: CornerRadius.medium))
```
