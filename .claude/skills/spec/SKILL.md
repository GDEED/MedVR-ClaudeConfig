---
name: spec
description: Document recently completed work by creating spec .md files in specs/ directory
---

# /spec - Document Recently Completed Work

Create specification documentation for recently completed work in this session.

## Instructions

When the user invokes `/spec`, you should:

1. **Review the conversation** to identify what was recently built, fixed, or modified
2. **Determine the appropriate spec type** based on the work:
   - **Immersive experience** → Create in `specs/immersive/`
   - **New feature/flow** → Create in `specs/features/`
   - **Architecture change** → Update `specs/architecture.md`
   - **General spec** → Create in `specs/`

3. **Create the spec file** with this structure:
   ```markdown
   # [Feature Name]

   **File(s):** `path/to/file.swift`
   **Type:** [View | Manager | Component | Flow]
   **Purpose:** One-line description

   ---

   ## Overview
   Brief description of what this does and why it exists.

   ## Implementation
   Key code patterns, setup, and usage.

   ## Integration Points
   How this connects to other parts of the codebase.

   ## Usage Example
   Code snippets showing how to use this feature.
   ```

4. **For immersive/RealityView specs**, include:
   - RealityView structure (content, update, attachments)
   - 3D entities created/modified
   - ARKit integration (hand tracking, image tracking)
   - Collision detection setup
   - Attachment positions and sizes

5. **Update the relevant README.md** if adding to a subdirectory

## Arguments

- `/spec [name]` - Create spec with specific name
- `/spec immersive` - Force creation in specs/immersive/
- `/spec update` - Update existing spec files based on recent changes
