# Design System Integration

## Overview

Style implementations should use design tokens from your project's design system, not hardcoded values. Each project defines its own token types — this document describes the categories and patterns.

## Token Categories

### Spacing
- xxSmall, xSmall, small, medium, large, xLarge, xxLarge

### Corner Radius
- none, small, medium, large, xLarge, full

### Border Width
- thin, medium, thick

### Sizes
- Component-specific enums (ButtonSize, IconSize, AvatarSize, etc.)

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
- Linear, angular, radial

### Symbol Assets
- **Icons** — enum with SF Symbols or custom assets
- **Illustrations** — enum with bundle assets

## Usage in Styles

Replace magic numbers with your project's design tokens:

```swift
struct DefaultCardStyle: CardStyle {
    @MainActor
    func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .padding(/* your spacing token */)
            .background(/* your background color token */)
            .clipShape(RoundedRectangle(cornerRadius: /* your radius token */))
    }
}
```

## Anti-Patterns

### **DON'T** use magic numbers
```swift
// WRONG
.padding(16)
.cornerRadius(8)
.foregroundColor(.gray)
```

### **DO** use design tokens
```swift
// CORRECT — adapt to your project's token API
.padding(spacing.medium)
.clipShape(RoundedRectangle(cornerRadius: cornerRadius.medium))
.foregroundStyle(colors.text.secondary)
```

## Integration Checklist

- [ ] Every padding uses a spacing token
- [ ] Every corner radius uses a radius token
- [ ] Every color uses a semantic color token
- [ ] Every font uses a typography token
- [ ] No hardcoded numeric values in style implementations
- [ ] Uses `foregroundStyle()` (not deprecated `foregroundColor()`)
- [ ] Uses `clipShape(RoundedRectangle(...))` (not deprecated `.cornerRadius()`)
