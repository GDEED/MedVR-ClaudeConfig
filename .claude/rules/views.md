---
globs:
  - "**/Views/**/*.swift"
  - "**/*View.swift"
  - "**/*View+*.swift"
description: SwiftUI view composition, state injection, and window management rules
---

# SwiftUI View Rules

## View Structure
- Keep views under 150 lines — extract subviews as separate structs (not `@ViewBuilder` functions)
- Use `@Environment` for shared state (e.g. your primary state model)
- Mark `@State` as `private` always
- Use `let` for read-only values passed from parent, `@Binding` only when child modifies parent state

## Immersive View Pattern
```swift
struct ImmersiveView: View {
    @Environment(YourStateModel.self) var appModel

    var body: some View {
        RealityView { content, attachments in
            // Setup entities
        } update: { content, attachments in
            // React to state changes
        } attachments: {
            // SwiftUI overlays
        }
    }
}
```

## Modern API Usage
- `foregroundStyle()` not `foregroundColor()`
- `clipShape(.rect(cornerRadius:))` not `cornerRadius()`
- `NavigationStack` not `NavigationView`
- `containerRelativeFrame()` or `visualEffect()` over `GeometryReader` where possible
- `.task(id:)` for async work tied to view lifecycle

## Window Management
```swift
@Environment(\.openWindow) var openWindow
@Environment(\.dismissWindow) var dismissWindow
```

## Performance
- No object creation or heavy computation in `body`
- Use `LazyVStack`/`LazyHStack` for large lists
- Stable identity for `ForEach` (never `.indices` for dynamic content)
- Prefer modifiers over conditional views for state changes (maintains view identity)
