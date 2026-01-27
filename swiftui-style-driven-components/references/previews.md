# Previews

## Structure

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

## Checklist

- [ ] All style variations together
- [ ] Different content configurations
- [ ] Environment inheritance and overrides
- [ ] Real-world usage scenarios
- [ ] Edge cases (empty, long text, empty content)

## Design System Environment

If design system requires setup:

```swift
extension View {
    func previewEnvironment() -> some View {
        self
            .environment(\.colorScheme, .light)
    }
}

#Preview("Styles") {
    CardPreviews.styles
        .previewEnvironment()
}
```
