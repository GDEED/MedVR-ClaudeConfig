---
globs:
  - "**/*Immersive*.swift"
  - "**/*AR*.swift"
  - "**/*Mixed*.swift"
  - "**/*Tracking*.swift"
  - "**/*RealityView*.swift"
  - "**/*Entity*.swift"
  - "**/*Component*.swift"
  - "**/*System*.swift"
  - "**/*App.swift"
description: visionOS immersive spaces, hand/image tracking, RealityView, and ECS patterns
---

# visionOS Patterns

## Immersive Spaces
```swift
ImmersiveSpace(id: "MyImmersiveSpace") { content }
    .immersionStyle(selection: $style, in: .mixed)  // AR overlay

@Environment(\.openImmersiveSpace) var openImmersiveSpace
await openImmersiveSpace(id: "MyImmersiveSpace")
```

Track state to prevent double-opens:
```swift
enum ImmersiveSpaceState { case closed, inTransition, open }
```

<!-- IF:HAND_TRACKING -->
## Hand Tracking
```swift
let session = ARKitSession()
let handTracking = HandTrackingProvider()
try await session.run([handTracking])

for await update in handTracking.anchorUpdates {
    guard update.anchor.isTracked else { continue }
    // Key joints: .wrist, .indexFingerTip, .thumbTip
    if let tip = update.anchor.handSkeleton?.joint(.indexFingerTip) { ... }
}
```
<!-- END:HAND_TRACKING -->

<!-- IF:IMAGE_TRACKING -->
## Image Tracking
```swift
guard let images = ReferenceImage.loadReferenceImages(inGroupNamed: "ARResource") else { return }
let imageTracking = ImageTrackingProvider(referenceImages: images)

for await update in imageTracking.anchorUpdates {
    switch update.event {
    case .added: placeContent(at: update.anchor.originFromAnchorTransform)
    case .updated: updateContent(with: update.anchor)
    case .removed: removeContent()
    }
}
```
<!-- END:IMAGE_TRACKING -->

## RealityView with Attachments
```swift
RealityView { content, attachments in
    let entity = try await Entity(named: "Scene", in: realityKitContentBundle)
    content.add(entity)
    if let label = attachments.entity(for: "label") {
        entity.addChild(label)
    }
} attachments: {
    Attachment(id: "label") {
        Text("Status").glassBackgroundEffect()
    }
}
```

## Head-Anchored UI
Use attachments positioned relative to user's head for persistent immersive UI.

<!-- IF:ECS -->
## ECS Custom Components
```swift
struct MyCustomComponent: Component {
    var state: String
    var stage: Int = 0
}
// Register in App.init(): MyCustomComponent.registerComponent()
```
<!-- END:ECS -->
