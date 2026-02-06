---
name: new-test
description: Create unit tests and E2E tests for recently completed work
---

# /new-test - Create Tests for Recently Completed Work

Generate unit tests and E2E tests for recently completed work in this session.

## Instructions

When the user invokes `/new-test`:

1. **Review the conversation** to identify what was recently built or modified
2. **Create test files** in appropriate locations:
   - Unit tests → `{{MODULE_NAME}}Tests/`
   - E2E tests → `{{MODULE_NAME}}UITests/`

3. **Use this naming convention:** `test_[what]_[condition]_[expected]`

## Test Structure

### Unit Test
```swift
import XCTest
@testable import {{MODULE_NAME}}

final class [Feature]Tests: XCTestCase {
    var sut: [SystemUnderTest]!

    override func setUp() {
        super.setUp()
        sut = [SystemUnderTest]()
    }

    func test_[action]_[condition]_[expectedResult]() {
        // Given
        // When
        // Then
    }
}
```

### E2E Test
```swift
import XCTest

final class [Flow]E2ETests: XCTestCase {
    var app: XCUIApplication!

    override func setUp() {
        super.setUp()
        app = XCUIApplication()
        app.launch()
    }

    func test_[userFlow]_[scenario]_[expectedOutcome]() {
        // Navigate, perform action, verify
    }
}
```

## Arguments

- `/new-test` - Create tests for all recent work
- `/new-test unit` - Unit tests only
- `/new-test e2e` - E2E tests only
- `/new-test [FeatureName]` - Tests for specific feature

## Run Command
```bash
xcodebuild test -scheme {{SCHEME_NAME}} -destination 'platform=visionOS Simulator,name=Apple Vision Pro'
```
