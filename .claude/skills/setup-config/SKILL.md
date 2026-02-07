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

#### `specs/DesignStyles.md` — Auto-Generated Design System

This spec is generated by scanning the user's existing codebase. It documents the actual design patterns in use so Claude can maintain visual consistency when building new views.

**Step 5a: Scan Codebase for Design Patterns**

Use **Glob** and **Grep** to discover the user's existing design language. Search the project root (not `.claude/` or `specs/`), targeting Swift source files.

1. **Find view files:**
   - Glob for `**/*.swift` in the project source directories
   - If no Swift files are found, skip this entire step and note in the summary that no existing codebase was detected

2. **Extract color usage** (Grep in Swift files for each pattern, `output_mode: "content"`, with 0-1 lines of context):
   - `Color\.` and `Color(` — custom and system colors
   - `\.foregroundStyle\(` — text/icon colors
   - `\.background\(` — background colors
   - `\.tint\(` — tint colors
   - `\.accentColor` or `\.accent` — accent color usage
   - Look for color definitions in extensions: `extension Color` or `static let/var` color properties
   - Check for `*.xcassets/**/Contents.json` color sets (Glob for `**/*.colorset/Contents.json`)

3. **Extract typography** (Grep in Swift files):
   - `\.font\(` — all font usage (system fonts, custom fonts, sizes, weights)
   - `Font\.custom\(` or `Font\.system\(` — custom font definitions
   - `\.fontWeight\(` and `\.bold()` — weight patterns
   - `\.fontDesign\(` — font design variants (rounded, monospaced, serif)

4. **Extract spacing and layout** (Grep in Swift files):
   - `\.padding\(` — padding values used
   - `spacing:` in `VStack`, `HStack`, `LazyVStack`, `LazyHStack` — stack spacing
   - `\.frame\(` — common frame dimensions
   - Look for spacing constants (e.g. `let spacing =`, `static let padding =`)

5. **Extract component styles** (Grep in Swift files):
   - `\.clipShape\(` and `cornerRadius` — corner radius values
   - `\.shadow\(` — shadow definitions
   - `\.glassBackgroundEffect` — visionOS glass usage
   - `\.buttonStyle\(` — button style usage
   - `ButtonStyle`, `ToggleStyle`, `LabelStyle` conformances — custom style definitions
   - `\.overlay\(` and `\.border\(` — border/overlay patterns
   - `\.opacity\(` — common opacity values

6. **Extract navigation and layout patterns** (Grep in Swift files):
   - `NavigationStack`, `NavigationSplitView` — navigation architecture
   - `TabView` — tab usage
   - `.ornament(` — visionOS ornament usage
   - `.toolbar(` — toolbar patterns

**Step 5b: Generate `specs/DesignStyles.md`**

Analyze the scan results and generate a structured design document. Group findings into categories and include real code snippets from the codebase. Use the template below, filling in discovered values and removing sections that had no findings.

```markdown
# [APP_NAME] Design System

Auto-generated from existing codebase during `/setup-config`.
Reference this document when building new views to maintain visual consistency.

---

## Color Palette

### Primary Colors
<!-- List the main colors found in the codebase -->
<!-- Example: -->
<!-- - **Accent**: `.blue` (used for primary actions, links) -->
<!-- - **Background**: `Color("AppBackground")` (main background) -->

| Role | Value | Usage |
|------|-------|-------|
| ... | ... | ... |

### Color Assets
<!-- List any custom colors found in .xcassets -->

### Color Patterns
```swift
// Document common color application patterns found
// e.g. how backgrounds, text, accents are typically applied
```

---

## Typography

### Font Scale
| Level | Font | Weight | Usage |
|-------|------|--------|-------|
| ... | ... | ... | ... |

### Custom Fonts
<!-- List any custom font families detected, or note "System fonts only" -->

### Typography Patterns
```swift
// Document common text styling patterns found
```

---

## Spacing & Layout

### Spacing Scale
<!-- List discovered spacing values, organized from smallest to largest -->
| Token | Value | Usage |
|-------|-------|-------|
| ... | ... | ... |

### Layout Patterns
- **Card padding**: [discovered value]pt
- **Section spacing**: [discovered value]pt
- **Element spacing**: [discovered value]pt
- **Corner radius**: [discovered value]pt

---

## Component Patterns

### Buttons
```swift
// Document button styles found (system styles + any custom ButtonStyle)
```

### Cards / Containers
```swift
// Document container patterns (padding, background, corner radius, shadow)
```

### Custom Styles
<!-- List any custom Style conformances found (ButtonStyle, ToggleStyle, etc.) -->

---

## visionOS-Specific

### Glass & Materials
<!-- Document .glassBackgroundEffect() usage, material patterns -->

### Ornaments
<!-- Document ornament usage if found -->

### Immersive UI
<!-- Document any immersive space UI patterns (attachment styling, 3D overlays) -->

---

## Navigation

### Architecture
<!-- NavigationStack / NavigationSplitView / TabView patterns -->

### Transitions
<!-- Any custom transition or animation patterns -->

---

## Conventions

### Naming
<!-- Observed naming conventions for style-related code -->

### File Organization
<!-- Where style/theme code lives in the project -->
```

**Important generation rules:**
- Use **actual values and code snippets** from the scan — do not use placeholder text
- **Remove sections entirely** if nothing was found for that category (don't leave empty sections with only HTML comments)
- If a section has findings, replace the HTML comments with the actual content
- **Consolidate similar values** — e.g. if padding of 16 appears 20 times, note it as the standard
- **Note inconsistencies** — if spacing varies wildly (e.g. padding 8, 12, 16, 20 all used frequently), flag this as something to standardize
- Include **file:line references** for key patterns so the user can verify
- If the codebase is brand new / minimal (fewer than 3 view files), generate a **starter design system** instead, based on the defaults in `.claude/rules/design-style.md`, and note that it should be updated as the app grows

#### Update `specs/immersive/README.md`
If any immersive features are enabled (HAND_TRACKING, IMAGE_TRACKING, or ECS), update the README to note which immersive capabilities are configured.

---

### Step 6: VERIFY & SUMMARY

#### 6a: Verify no remaining template tokens

Use **Grep** to search all `.md` files for `{{` — warn about any remaining unreplaced tokens.

#### 6b: Verify IF/END markers are clean

If feature processing was done, verify no orphaned `<!-- IF:` or `<!-- END:` markers remain.

#### 6c: Verify CLAUDE.md cross-references specs

Read `CLAUDE.md` and verify it contains a `## Project Documentation` section that references:
- `specs/PRD.md`
- `specs/architecture.md`
- `specs/DesignStyles.md`
- `specs/testing.md`

If the section is missing (e.g., an older template version), add it after the `## Architecture` section:

```markdown
## Project Documentation
- `specs/PRD.md` — Product requirements, feature scope, and user flows
- `specs/architecture.md` — App-specific architecture, data flow, and file organization
- `specs/DesignStyles.md` — Project design system (overrides defaults in .claude/rules/design-style.md)
- `specs/testing.md` — Test structure, run commands, and naming conventions

> **Always check specs/ before building new features** to stay aligned with project requirements and existing patterns.
```

Also verify the `## Key Concepts` section includes the PRD reference comment. If not, add:
```markdown
<!-- See specs/PRD.md for full product requirements and feature scope -->
```

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

Design system: [Generated from codebase / Starter template]

Next steps:
  1. Review CLAUDE.md for accuracy
  2. Add your app's key concepts to CLAUDE.md
  3. Fill in specs/PRD.md with your product requirements
  4. Review specs/DesignStyles.md and refine as needed
  5. Run /review-h to validate your codebase
  6. Start building!
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
