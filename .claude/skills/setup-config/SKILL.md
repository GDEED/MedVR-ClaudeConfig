---
name: setup-config
description: Configure this visionOS Claude Code template for your project - replaces placeholders, enables features, generates specs
user_invocable: true
---

# /setup-config - visionOS Project Configuration

Configure this Claude Code template for a specific visionOS project.

## Instructions

When the user invokes `/setup-config`, execute the following steps in order.

---

### Step 1: WELCOME

Print this banner:

```
══════════════════════════════════════
  VISIONOS PROJECT CONFIG
══════════════════════════════════════
Let's configure this Claude setup for your project.
```

---

### Step 2: PROJECT INFO

Use `AskUserQuestion` to collect project details. Ask these in 2 rounds:

**Round 1** (use AskUserQuestion with free-text — ask the user to provide):
Prompt the user with a single message asking for all of these:
- **App name** (display name, e.g. "MyApp")
- **One-line description** (e.g. "A visionOS fitness app")
- **Xcode scheme name** (usually same as app name)
- **Module name** (usually same as scheme name)
- **Bundle ID** (e.g. "com.company.myapp")
- **Min platform version** (default: "visionOS 2.0+")

Store these as:
- `APP_NAME`
- `APP_DESCRIPTION`
- `SCHEME_NAME`
- `MODULE_NAME`
- `BUNDLE_ID`
- `MIN_PLATFORM`

---

### Step 3: FEATURE SELECTION

Use `AskUserQuestion` with `multiSelect: true`:

**Question:** "Which features does your app use?"

**Options:**
1. **SharePlay / Multiplayer** — GroupActivities, shared sessions, spatial personas
2. **Hand Tracking (ARKit)** — Hand gesture detection, skeleton tracking
3. **Image Tracking (ARKit)** — Reference image detection and anchor placement
4. **RealityKit ECS** — Custom Component and System types

Store selected features as flags: `SHAREPLAY`, `HAND_TRACKING`, `IMAGE_TRACKING`, `ECS`

---

### Step 4: APPLY CONFIGURATION

Perform these operations:

#### 4a: Replace Placeholder Tokens

Use **Grep** to find all files containing `{{` in the `.claude/` directory and `CLAUDE.md`. For each file found, use **Read** then **Edit** to replace all occurrences of:

| Token | Replacement |
|-------|-------------|
| `{{APP_NAME}}` | User's app name |
| `{{APP_DESCRIPTION}}` | User's description |
| `{{SCHEME_NAME}}` | User's scheme name |
| `{{MODULE_NAME}}` | User's module name |
| `{{BUNDLE_ID}}` | User's bundle ID |
| `{{MIN_PLATFORM}}` | User's min platform |

Use `replace_all: true` for each token replacement within each file.

#### 4b: Process Feature Flags

For each feature flag (`SHAREPLAY`, `HAND_TRACKING`, `IMAGE_TRACKING`, `ECS`):

**If the feature is DISABLED:**
- Find all files containing `<!-- IF:FEATURE_NAME -->` using Grep
- Read each file and remove the entire block including markers:
  ```
  <!-- IF:FEATURE_NAME -->
  ...content...
  <!-- END:FEATURE_NAME -->
  ```
- Use Edit to remove the block (markers + content between them)

**If the feature is ENABLED:**
- Find all files containing `<!-- IF:FEATURE_NAME -->` using Grep
- Read each file and remove ONLY the marker comments, keeping the content:
  - Remove `<!-- IF:FEATURE_NAME -->` lines
  - Remove `<!-- END:FEATURE_NAME -->` lines
  - Keep everything between them

---

### Step 5: GENERATE SPECS

Based on project info and selected features, generate spec files:

#### `specs/PRD.md`
```markdown
# [APP_NAME] - Product Requirements Document

## Overview

[APP_DESCRIPTION] for Apple Vision Pro.

**Target Platform:** [MIN_PLATFORM]

---

## Core Features

<!-- List your app's core features here -->

---

## User Flows

<!-- Document key user flows here -->

---

## Technical Requirements

### Platform
- [MIN_PLATFORM]
- Swift 6 strict concurrency
- SwiftUI + RealityKit

[IF SHAREPLAY: ### Multiplayer
- SharePlay via GroupActivities
- Spatial personas support
- Real-time state synchronization]

[IF HAND_TRACKING: ### Hand Tracking
- ARKit hand tracking integration
- Gesture detection and response]

[IF IMAGE_TRACKING: ### Image Tracking
- ARKit reference image detection
- Anchor-based content placement]

[IF ECS: ### Entity Component System
- Custom RealityKit Components
- Custom RealityKit Systems
- Component registration in App.init()]

---

## Future Considerations

<!-- Planned features and improvements -->
```

#### `specs/architecture.md`
```markdown
# [APP_NAME] Architecture

## State Management

### @Observable Models
```swift
@Observable @MainActor class AppModel { }
```

**Usage in views:**
```swift
@Environment(AppModel.self) private var appModel
```

---

## Data Flow

<!-- Document your app's data flow here -->

---

## File Organization

<!-- Document your file structure here. Use /review-h files to auto-generate. -->

[IF SHAREPLAY: ## SharePlay Integration
- GroupActivity definition
- Session lifecycle management
- Message types and routing]

[IF HAND_TRACKING: ## Hand Tracking
- ARKit session management
- Hand anchor processing
- Gesture detection]

[IF IMAGE_TRACKING: ## Image Tracking
- Reference image configuration
- Anchor placement and updates]

[IF ECS: ## Entity Component System
- Component definitions
- System implementations
- Registration in App.init()]
```

#### `specs/testing.md`
```markdown
# [APP_NAME] Testing Guide

Use `/new-test` to generate tests for recently completed work.

## Test Structure

```
[MODULE_NAME]/
├── [MODULE_NAME]Tests/         # Unit tests
│   └── ...
└── [MODULE_NAME]UITests/       # E2E tests
    └── ...
```

## Running Tests

```bash
# Run all tests
xcodebuild test -scheme [SCHEME_NAME] -destination 'platform=visionOS Simulator,name=Apple Vision Pro'

# Run specific test file
xcodebuild test -scheme [SCHEME_NAME] -destination 'platform=visionOS Simulator,name=Apple Vision Pro' -only-testing:[MODULE_NAME]Tests/SomeTests
```

## Naming Convention

`test_[what]_[condition]_[expected]`

```swift
// Good
func test_someMethod_givenCondition_expectedResult()

// Bad
func testSomething()
```
```

Replace `[APP_NAME]`, `[MODULE_NAME]`, `[SCHEME_NAME]`, `[MIN_PLATFORM]` with actual values (not template tokens — use the real values collected in Step 2).

Only include `[IF FEATURE: ...]` sections for features the user selected. Remove the `[IF FEATURE: ...]` wrapper text — just include or exclude the content.

#### Update `specs/immersive/README.md`
If any immersive features are enabled (HAND_TRACKING, IMAGE_TRACKING, or ECS), update the README to note which immersive capabilities are configured.

---

### Step 6: VERIFY & SUMMARY

#### 6a: Verify no remaining template tokens

Use **Grep** to search all `.md` files for `{{` — warn about any remaining unreplaced tokens.

#### 6b: Verify IF/END markers are clean

If feature processing was done, verify no orphaned `<!-- IF:` or `<!-- END:` markers remain.

#### 6d: Print summary

```
══════════════════════════════════════
  SETUP COMPLETE
══════════════════════════════════════
App: [APP_NAME]
Description: [APP_DESCRIPTION]
Scheme: [SCHEME_NAME]
Bundle ID: [BUNDLE_ID]
Platform: [MIN_PLATFORM]

Features enabled:
  ✓ SharePlay          (or ✗ if disabled)
  ✓ Hand Tracking      (or ✗ if disabled)
  ✓ Image Tracking     (or ✗ if disabled)
  ✓ ECS Components     (or ✗ if disabled)

Files configured: [count]
Specs generated: [count]

Next steps:
  1. Review CLAUDE.md for accuracy
  2. Add your app's key concepts to CLAUDE.md
  3. Run /review-h to validate your codebase
  4. Start building!
══════════════════════════════════════
```

---

## Critical Rules

- **Never modify .swift files** — this skill only configures .md files
- **Always use Edit with replace_all: true** for token replacement to catch all occurrences
- **Process tokens BEFORE feature flags** — tokens inside IF blocks need replacement first
- **Read files before editing** — always Read a file before using Edit on it
- **Use real values in generated specs** — don't leave template tokens in generated files
- **Warn but don't fail** on verification issues — let the user fix manually if needed
