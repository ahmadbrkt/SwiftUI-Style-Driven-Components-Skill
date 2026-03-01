# Documentation

Follow [Apple's Swift documentation conventions](https://www.swift.org/documentation/docc/writing-symbol-documentation-in-your-source-files). Every public symbol gets a doc comment.

## Component

Start with a single-sentence summary. Add an overview if behavior needs explanation. Use 4-space indented code for examples.

```swift
/// A control that initiates an action.
///
/// Use `Card` to create a tappable surface styled via the environment.
/// Apply styles using the ``cardStyle(_:)`` modifier.
///
///     Card("Save", action: { save() })
///         .cardStyle(.compact)
///
/// Style an entire subtree by applying the modifier to a parent:
///
///     VStack {
///         Card("First", action: {})
///         Card("Second", action: {})
///     }
///     .cardStyle(.outlined)
///
public struct Card<Content>: View where Content: View {
```

## Style Protocol

```swift
/// A type that defines the visual appearance of a card.
///
/// Conform to this protocol to create custom card styles.
/// Use ``CardStyleConfiguration`` to access the card's content.
///
///     struct BrandedCardStyle: CardStyle {
///         func makeBody(configuration: Configuration) -> some View {
///             configuration.content
///                 .padding()
///                 .background(.blue)
///         }
///     }
///
public protocol CardStyle: DynamicProperty {
```

## Initializers

Use `- Parameters:` for multiple parameters, `- Parameter name:` for one:

```swift
/// Creates a card with a custom label.
///
/// - Parameters:
///   - action: The action to perform when the user taps the card.
///   - label: A view builder that produces the card's content.
public init(
    action: @escaping @MainActor () -> Void,
    @ViewBuilder label: () -> Label
) {
```

## Convenience Initializers

```swift
/// Creates a card with a localized title.
///
/// - Parameters:
///   - titleKey: The localized string key for the card's title.
///   - action: The action to perform when the user taps the card.
public init(
    _ titleKey: LocalizedStringKey,
    action: @escaping @MainActor () -> Void
) {
```

## Configuration Properties

```swift
public struct CardStyleConfiguration {

    /// The card's content.
    public let content: Content

    /// Whether the card is currently pressed.
    public let isPressed: Bool
}
```

## Environment Modifier

```swift
extension View {
    /// Sets the style for cards within this view hierarchy.
    ///
    /// - Parameter style: The card style to apply.
    /// - Returns: A view with the card style set in its environment.
    public func cardStyle(_ style: some CardStyle) -> some View {
```

## Style Implementations

```swift
/// A card style with a rounded border and no fill.
///
/// Use ``CardStyle/outlined`` to apply this style.
public struct OutlinedCardStyle: CardStyle {
```

## Convenience Accessors

```swift
extension CardStyle where Self == OutlinedCardStyle {
    /// A card style with a rounded border and no fill.
    public static var outlined: OutlinedCardStyle { .init() }
}
```

## Rules

- Every `public` symbol has a `///` doc comment
- First line is a single-sentence summary (no blank line before overview)
- Use `- Parameter name:` (singular) or `- Parameters:` (multiple)
- Use `- Returns:` for non-Void return values
- Use `- Throws:` for throwing functions
- Link related symbols with double backticks: `` ``CardStyle`` ``
- Code examples use 4-space indentation (not fenced blocks)
