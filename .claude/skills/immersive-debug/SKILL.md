---
name: immersive-debug
description: CLI-style debugger for RealityView and immersive visionOS features
---

# /immersive-debug - Immersive Debugging CLI

Focused debugging tool for RealityView and immersive features. Minimal conversation, strict flow.

## Mode

**CLI MODE** - Short, direct answers. One question at a time.

## Flow

### Step 1: Target Selection
```
══════════════════════════════════════
  IMMERSIVE DEBUGGER
══════════════════════════════════════

What are you debugging?

  [1] Hand tracking not working
  [2] Collision not detecting
  [3] Entity not visible
  [4] Attachment not showing
  [5] Image tracking issue
  [6] Space won't open/close
  [7] Other

Select [1-7]:
```

### Step 2: Run Diagnostic

Based on selection, execute focused checks:

**[1] Hand Tracking:**
- HandTrackingProvider.isSupported?
- session.run([handTracking]) called?
- continuousHandUpdate() running?
- Authorization requested?

**[2] Collision:**
- Entity names correct?
- CollisionComponent present?
- Mode = .trigger?
- setupCollisionSubscription() called?

**[3] Entity Visibility:**
- Position reasonable?
- Scale not zero?
- Added to content?
- Material assigned?

**[4] Attachment:**
- ID matches exactly?
- attachments.entity(for:) called?
- Added to scene?

**[5] Image Tracking:**
- Reference images in ARResource?
- Provider initialized?
- anchor.isTracked checked?

**[6] Space Lifecycle:**
- ID matches ImmersiveSpace?
- Not already in transition?
- Using await?

### Step 3: Fix

```
ISSUE IDENTIFIED
────────────────
File:   [path:line]
Fix:    [what to change]

Apply fix? [Y/N]
```

## Commands

- `skip` - Skip current check
- `tree` - Print entity hierarchy
- `exit` - End session
