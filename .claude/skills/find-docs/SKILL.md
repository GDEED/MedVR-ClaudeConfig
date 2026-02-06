---
name: find-docs
description: Search Apple Developer Documentation using apple-docs MCP for visionOS, RealityKit, ARKit, SwiftUI
---

# /find-docs - Search Apple Developer Documentation

Quick search for Apple docs using the apple-docs MCP.

## Usage

```
/find-docs [query]
```

## Examples

```
/find-docs RealityView attachments
/find-docs HandTrackingProvider
/find-docs ImmersiveSpace lifecycle
/find-docs CollisionComponent
/find-docs WWDC24 RealityKit
```

## Instructions

When invoked, use the apple-docs MCP to search and return:

```
══════════════════════════════════════
  APPLE DOCS: [query]
══════════════════════════════════════

📄 [API Name]
   Platform: visionOS 1.0+

   [Brief description]

   Key points:
   • [point 1]
   • [point 2]

   ```swift
   [minimal example]
   ```

🎬 WWDC Session (if relevant)
   [Session name] - [year]

══════════════════════════════════════
```

## Shortcuts

| Query | Searches |
|-------|----------|
| `/find-docs rk [term]` | RealityKit |
| `/find-docs ark [term]` | ARKit |
| `/find-docs sui [term]` | SwiftUI |
| `/find-docs wwdc [term]` | WWDC sessions |

## Notes

- Prioritize visionOS results
- Include code examples when available
- Keep responses brief
