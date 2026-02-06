---
globs:
  - "**/SessionController*.swift"
  - "**/*Activity*.swift"
  - "**/*SharePlay*.swift"
  - "**/*Message*.swift"
  - "**/*Session*.swift"
description: SharePlay GroupActivity, session lifecycle, messaging, spatial personas
---

<!-- IF:SHAREPLAY -->
# SharePlay & Multiplayer Rules

## GroupActivity Definition
```swift
struct {{APP_NAME}}Activity: GroupActivity {
    static let activityIdentifier = "{{BUNDLE_ID}}.activity"
    var metadata: GroupActivityMetadata {
        var meta = GroupActivityMetadata()
        meta.title = "{{APP_NAME}}"
        meta.type = .generic
        return meta
    }
}
```

## Session Lifecycle
```swift
// Start
let activity = {{APP_NAME}}Activity()
let result = await activity.prepareForActivation()
if case .activationPreferred = result {
    _ = try await activity.activate()
}

// Observe
for await session in {{APP_NAME}}Activity.sessions() {
    configureSession(session)
}
```

## Messaging
All messages must be `Codable` and `Sendable`:
```swift
struct StateChangedMessage: Codable, Sendable {
    let senderId: UUID
    let newState: String
}

let messenger = GroupSessionMessenger(session: session)
try await messenger.send(message, to: .all)

for await (message, _) in messenger.messages(of: StateChangedMessage.self) {
    handle(message)
}
```

## Spatial Personas (visionOS)
```swift
let coordinator = await session.systemCoordinator
coordinator.configuration.supportsGroupImmersiveSpace = true

for await state in coordinator.localParticipantStates {
    if state.isSpatial {
        coordinator.assignSeat(seatID, to: state.participant)
    }
}
```

## State Synchronization
- Host assigns state/roles to participants
- Use session controller to sync state changes across all participants
- Validate state availability before assignment
<!-- END:SHAREPLAY -->
