---
globs:
  - "**/*Model*.swift"
  - "**/*Manager*.swift"
  - "**/*Controller*.swift"
  - "**/*Component*.swift"
  - "**/*System*.swift"
  - "**/*App.swift"
description: Architecture rules for state management, ECS components, and data flow
---

# Architecture Rules

## State Management
- Use `@Observable` for all state classes — never `ObservableObject`/`@Published`
- Mark `@Observable` classes with `@MainActor` unless using a different actor
- Single source of truth: one primary state model owns app state
<!-- IF:SHAREPLAY -->
- Session controller owns SharePlay state separately from app state
<!-- END:SHAREPLAY -->
- Use `@Environment(TypeName.self)` to inject `@Observable` objects, not `@EnvironmentObject`

<!-- IF:ECS -->
## Component Registration
Register all RealityKit components and systems in `App.init()`:
```swift
MyCustomComponent.registerComponent()
MyCustomSystem.registerSystem()
```
<!-- END:ECS -->

## Swift 6 Concurrency
- Prefer structured concurrency (`async let`, task groups) over `Task.detached`
<!-- IF:SHAREPLAY -->
- All SharePlay messages: `Codable & Sendable`
<!-- END:SHAREPLAY -->
- Don't blanket `@MainActor` — justify isolation per type
- Use `@unchecked Sendable` only as last resort with documented safety invariant
