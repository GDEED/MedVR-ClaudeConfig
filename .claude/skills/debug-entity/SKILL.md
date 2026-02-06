---
name: debug-entity
description: Inspect RealityKit entity hierarchy, components, positions, and collision setup
---

# /debug-entity - RealityKit Entity Inspector

Inspect RealityKit entity hierarchy, components, and collision setup.

## Usage

```
/debug-entity [entityName]
```

## Instructions

When invoked, find the entity and print inspection report:

```
═══════════════════════════════════════════════════════════
ENTITY: [name]
═══════════════════════════════════════════════════════════
Source:     [file:line]
Parent:     [parent entity name]
Position:   SIMD3<Float>(x, y, z)
Scale:      SIMD3<Float>(x, y, z)
═══════════════════════════════════════════════════════════
COMPONENTS:
  ✓ CollisionComponent (mode: .trigger)
  ✓ PhysicsBodyComponent (mode: .kinematic)
  ✓ InputTargetComponent
  ✗ HoverEffectComponent (missing)
═══════════════════════════════════════════════════════════
CHILDREN: (3)
  ├── ChildEntity1
  └── ChildEntity2
═══════════════════════════════════════════════════════════
ISSUES:
  ⚠ No collision subscription found
═══════════════════════════════════════════════════════════
```

## Arguments

- `/debug-entity` - List all major entities
- `/debug-entity [name]` - Inspect specific entity
- `/debug-entity hands` - Inspect hand entities
- `/debug-entity attachments` - List RealityView attachments

## Entity Discovery

On first run, use Glob and Grep to discover all entities in the project and build a reference table:

1. **Glob** for `**/*.swift` files
2. **Grep** for entity creation patterns: `Entity(named:`, `ModelEntity(`, `.findEntity(named:`, `entity.name =`
3. Build and display a table of discovered entities with their file locations and purposes
4. Cache this table for the session
