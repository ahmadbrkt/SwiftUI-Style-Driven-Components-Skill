# Testing

## Framework

Use **Swift Testing** with **SnapshotTesting**:

```swift
import SwiftUI
import Testing
import SnapshotTesting
@testable import ComponentLibrary
```

## Test File Structure

Organize tests into multiple `@Suite` blocks per file — one for snapshots, one per domain:

```swift
// MARK: - Snapshot Tests

@Suite(.snapshots(record: .never)) @MainActor
struct CardSnapshotTests {
    // Visual regression tests
}

// MARK: - Size Tests (if size enum exists)

@Suite("CardSize")
struct CardSizeTests {
    // Dimension value tests
}
```

## Snapshot Tests

**Every snapshot test consumes a preview.** One `@Test` per preview static var:

```swift
@Suite(.snapshots(record: .never)) @MainActor
struct CardSnapshotTests {

    @Test
    func styles() {
        assertSnapshot(of: CardPreviews.styles, as: .image)
    }

    @Test
    func content() {
        assertSnapshot(of: CardPreviews.content, as: .image)
    }

    @Test
    func cascading() {
        assertSnapshot(of: CardPreviews.cascading, as: .image)
    }

    @Test
    func edgeCases() {
        assertSnapshot(of: CardPreviews.edgeCases, as: .image)
    }
}
```

### With Custom Layout and Traits

If your project defines snapshot layout helpers, use them:

```swift
@Test
func styles() {
    assertSnapshot(
        of: CardPreviews.styles,
        as: .image(
            layout: .fixed(width: 393, height: 852),
            traits: .init(userInterfaceStyle: .light)
        )
    )
}
```

### Snapshot Rules

- One `@Test` per preview static var
- Test function name matches preview property name (camelCase)
- Record mode: `@Suite(.snapshots(record: .all))` — **temporary, never commit**
- Verify mode: `@Suite(.snapshots(record: .never))` — **default**

## Size/Token Tests

Test dimension values for size enums:

```swift
@Suite("CardSize")
struct CardSizeTests {

    @Test
    func height_valuesAreCorrect() {
        #expect(CardSize.small.height == 32)
        #expect(CardSize.medium.height == 40)
        #expect(CardSize.large.height == 48)
        #expect(CardSize.custom(60).height == 60)
    }

    @Test
    func cornerRadius_valuesAreCorrect() {
        #expect(CardSize.small.cornerRadius == 16)
        #expect(CardSize.medium.cornerRadius == 20)
        #expect(CardSize.large.cornerRadius == 24)
    }
}
```

## Recording Snapshots

```swift
// Record mode (temporary — remove before committing)
@Suite(.snapshots(record: .all)) @MainActor

// Verify mode (default — committed code)
@Suite(.snapshots(record: .never)) @MainActor
```

## Coverage Checklist

- [ ] Snapshot test for every preview static var
- [ ] Size/dimension token values tested (if size enum exists)

See `references/previews.md` for preview organization that feeds snapshots.
