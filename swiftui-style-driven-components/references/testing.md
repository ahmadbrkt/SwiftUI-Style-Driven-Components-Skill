# Testing

## Framework

Use **Swift Testing** with **SnapshotTesting**:

```swift
import SwiftUI
import Testing
import SnapshotTesting
@testable import ComponentLibrary
```

## Snapshot Tests

Leverage previews for comprehensive coverage:

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

## Recording Snapshots

```swift
// Record mode (temporary)
@Suite(.snapshots(record: .all)) @MainActor

// Verify mode (default)
@Suite(.snapshots(record: .never)) @MainActor
```

## Preview Coverage

Ensure previews cover:
- All style variations
- Content configurations
- Environment cascading
- Edge cases (empty, long text)

See `references/previews.md` for preview organization.
