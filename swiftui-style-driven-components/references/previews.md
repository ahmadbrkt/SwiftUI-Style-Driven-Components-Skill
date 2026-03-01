# Previews

## Structure

Use `#Preview` macros mapped to `enum` static vars:

```swift
import SwiftUI

#Preview("Styles") {
    CardPreviews.styles
}

#Preview("Content") {
    CardPreviews.content
}

#Preview("Cascading") {
    CardPreviews.cascading
}

#Preview("In Context") {
    CardPreviews.inContext
}

#Preview("Edge Cases") {
    CardPreviews.edgeCases
}

public enum CardPreviews {
    
    @MainActor public static var styles: some View {
        VStack(spacing: 16) {
            Card("Default", action: { })
                .cardStyle(.automatic)
            
            Card("Compact", action: { })
                .cardStyle(.compact)
            
            Card("Outlined", action: { })
                .cardStyle(.outlined)
        }
        .padding()
    }
    
    @MainActor public static var content: some View {
        VStack(spacing: 16) {
            Card("Text Only", action: { })
            
            Card(action: { }) {
                Label("With Icon", systemImage: "star")
            }
        }
        .padding()
    }
    
    @MainActor public static var cascading: some View {
        VStack(spacing: 8) {
            Card("Inherits", action: { })
            
            Card("Overrides", action: { })
                .cardStyle(.automatic)
        }
        .cardStyle(.compact)
        .padding()
    }
    
    @MainActor public static var inContext: some View {
        VStack(spacing: 8) {
            ForEach(1...3, id: \.self) { i in
                Card("Item \(i)", action: { })
            }
        }
        .cardStyle(.compact)
        .padding()
    }
    
    @MainActor public static var edgeCases: some View {
        VStack(spacing: 16) {
            Card("", action: { })
            
            Card(String(repeating: "Long ", count: 20), action: { })
            
            Card(action: { }) { EmptyView() }
        }
        .padding()
    }
}
```

## Preview Scaffold (Optional)

If your design system requires setup (e.g., registering custom fonts, constraining to device width), create a reusable view modifier and apply it to every preview root. The exact implementation depends on your project.

## Section Labels (Optional)

For previews with many variants, a helper view that renders a caption above each group can reduce noise. Group related variants under descriptive labels like "At Minimum (0)" or "With Icon".

## Stateful Helper Views

For components that require `@Binding`, create **private helper views** inside the enum:

```swift
enum StepperPreviews {

    @MainActor static var styles: some View {
        VStack(spacing: 16) {
            StepperPreview(value: 0)
            StepperPreview(value: 5)
            StepperPreview(value: 10, range: 0...10)
        }
        .padding()
    }

    // MARK: - Helper Views

    private struct StepperPreview: View {
        @State var value: Int
        var range: ClosedRange<Int>?

        init(value: Int, range: ClosedRange<Int>? = nil) {
            _value = State(initialValue: value)
            self.range = range
        }

        var body: some View {
            Stepper(value: $value, in: range)
        }
    }
}
```

**Never** use `@State` directly in the `enum` — it won't work. Always use a private helper view.

## Design System Environment

If your design system requires environment setup:

```swift
extension View {
    func previewEnvironment() -> some View {
        self
            .environment(\.colorScheme, .light)
            // Add any project-specific environment setup
    }
}

#Preview("Styles") {
    CardPreviews.styles
        .previewEnvironment()
}
```

## Checklist

- [ ] Preview file uses `enum` (not struct/class)
- [ ] `@MainActor` on every `static var`
- [ ] `#Preview("Name")` macro for each static var
- [ ] All style variations covered
- [ ] Different content configurations
- [ ] Environment cascading + override demonstrated
- [ ] Real-world usage scenarios
- [ ] Edge cases: empty string, long text, EmptyView
- [ ] Disabled states (if applicable)
- [ ] All sizes (if size environment key exists)
- [ ] Stateful content uses private helper views (not `@State` in enum)
- [ ] MARK comments organize sections

## Preview ↔ Snapshot Mapping

Every preview static var becomes one snapshot test:

| Preview | Test |
|---|---|
| `CardPreviews.styles` | `func styles()` |
| `CardPreviews.content` | `func content()` |
| `CardPreviews.cascading` | `func cascading()` |
| `CardPreviews.edgeCases` | `func edgeCases()` |

See `references/testing.md` for snapshot test patterns.
