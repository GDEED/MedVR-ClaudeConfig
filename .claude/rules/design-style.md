---
globs:
  - "**/Views/**/*.swift"
  - "**/*View.swift"
  - "**/*Style*.swift"
  - "**/*Metrics*.swift"
description: Design system — typography, spacing, component patterns, visionOS UI
---

# Design System

> **Project-specific styles**: If `specs/DesignStyles.md` exists, prefer the project-specific values there over the defaults below. That file is auto-generated from the codebase during `/setup-config`.

## Typography

### Hierarchy
- **Titles**: `.largeTitle` or `.title` with `.bold()` - Screen headers, key labels
- **Headings**: `.headline` - Section headers
- **Body**: `.body` - Instructions, descriptions
- **Metrics**: `.system(.title, design: .rounded).monospacedDigit()` - Numeric displays, timers
- **Captions**: `.caption` - Timestamps, secondary info

### Numeric Readability
```swift
// High-contrast for key metrics
Text("120")
    .font(.system(.title, design: .rounded))
    .monospacedDigit()
    .fontWeight(.semibold)
```

## Color Palette
- **Accent**: `.accentColor` / system blue — primary actions, links, interactive elements
- **Primary text**: `.primary` — main content
- **Secondary text**: `.secondary` — supporting content, timestamps
- **Destructive**: `.red` — delete actions, error states
- **Success**: `.green` — completion, positive status

### Glass Tinting
```swift
// Tinted glass panel
content
    .padding(16)
    .glassBackgroundEffect()
    .overlay(Color.accentColor.opacity(0.1))
```

> See `specs/DesignStyles.md` for project-specific colors when available.

## Component Patterns

### Badge
```swift
Text(label)
    .font(.caption.weight(.semibold))
    .padding(.horizontal, 12)
    .padding(.vertical, 6)
    .background(color.opacity(0.2))
    .foregroundStyle(color)
    .clipShape(Capsule())
```

### Primary Action Button
```swift
Button(action: {}) {
    Text("Start")
        .font(.headline)
        .frame(maxWidth: .infinity)
        .padding(.vertical, 14)
}
.buttonStyle(.borderedProminent)
```

### Status Indicator
```swift
HStack(spacing: 8) {
    Circle()
        .fill(status.color)
        .frame(width: 10, height: 10)
    Text(status.label)
        .font(.subheadline)
}
```

### Progress Steps
```swift
HStack(spacing: 12) {
    ForEach(0..<totalSteps, id: \.self) { step in
        Circle()
            .fill(step <= currentStep ? Color.accentColor : Color.secondary.opacity(0.3))
            .frame(width: 12, height: 12)
    }
}
```

## Spacing & Layout
- Card padding: 16-20pt
- Section spacing: 24pt
- Element spacing: 8-12pt
- Corner radius: 12-16pt (cards), 8pt (buttons), full (badges)

## Immersive Space UI
- Use `.glassBackgroundEffect()` for floating panels
- High contrast text over 3D content
- Subtle shadows for depth: `.shadow(color: .black.opacity(0.2), radius: 8)`
- Keep UI minimal - don't obscure the 3D content

## Accessibility
- Minimum 4.5:1 contrast for text
- Use SF Symbols with `.accessibilityLabel()`
- Support Dynamic Type
- Critical status uses color + icon + text (never color alone)
