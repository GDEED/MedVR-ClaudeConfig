---
globs:
  - "**/*Tests.swift"
  - "**/*Test.swift"
  - "**/*Tests/**/*.swift"
  - "**/*UITests/**/*.swift"
description: Unit test and E2E test conventions, mocking strategies
---

# Testing Rules

## Unit Tests
- Test ViewModels and business logic in isolation
- Mock dependencies (network, ARKit sessions, SharePlay)
- One assertion per test when possible
- Name tests: `test_methodName_condition_expectedResult`

## What to Test
- **ViewModels**: State changes, computed properties
- **Services**: Data transformations, business logic
- **Models**: Encoding/decoding, validation
- **Managers**: State transitions in manager classes

## E2E Tests (XCUITest)
- Test critical user flows
- Keep tests independent and idempotent
- Focus on happy path + key error states

## Mocking ARKit/RealityKit
Since ARKit requires device, mock at the data layer:
```swift
protocol HandTrackingDataSource {
    var anchorUpdates: AsyncStream<HandAnchorUpdate> { get }
}

class MockHandTrackingDataSource: HandTrackingDataSource {
    // Provide test data
}
```

<!-- IF:SHAREPLAY -->
## SharePlay Testing
- Test message encoding/decoding
- Test SessionController state transitions
- Use mock GroupSession for unit tests
<!-- END:SHAREPLAY -->
