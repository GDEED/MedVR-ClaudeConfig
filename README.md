# visionOS Claude Code Template

A pre-configured Claude Code setup for visionOS development. Drop it into your project to get rules, skills, agents, and MCP tools tailored for building Apple Vision Pro apps with SwiftUI + RealityKit.

## What's Included

```
CLAUDE.md                          # Project instructions (configurable)
.mcp.json                          # Apple Developer Docs MCP server
.claude/
├── rules/                         # Coding conventions auto-loaded by Claude
│   ├── architecture.md            # State management, concurrency, ECS
│   ├── design-style.md            # Typography, components, spacing
│   ├── shareplay-multiplayer.md   # GroupActivities patterns
│   ├── testing.md                 # Unit & E2E testing conventions
│   ├── views.md                   # SwiftUI view rules
│   └── visionos-patterns.md      # Immersive spaces, hand/image tracking
├── skills/                        # Slash commands you can invoke
│   ├── setup-config/              # /setup-config — configure for your project
│   ├── debug-entity/              # /debug-entity — inspect RealityKit entities
│   ├── debug-h/                   # /debug-h — structured debugging mentor
│   ├── find-docs/                 # /find-docs — search Apple docs via MCP
│   ├── immersive-debug/           # /immersive-debug — debug immersive spaces
│   ├── new-test/                  # /new-test — generate tests
│   ├── review-h/                  # /review-h — code review report card
│   └── spec/                      # /spec — document completed work
├── agents/
│   └── swiftui-visionos.md       # Specialized SwiftUI/visionOS agent
├── settings.json                  # SwiftUI Expert plugin config
└── settings.local.json            # Permission presets
```

## Getting Started

### 1. Clone into your project

```bash
# From your visionOS project root
git clone https://github.com/GDEED/MedVR-ClaudeConfig.git .claude-template
cp -r .claude-template/.claude .
cp .claude-template/CLAUDE.md .
cp .claude-template/.mcp.json .
cp -r .claude-template/specs .
rm -rf .claude-template
```

Or copy the files manually — you just need `CLAUDE.md`, `.mcp.json`, `.claude/`, and `specs/` at your project root.

### 2. Install Swift LSP

Install [SourceKit-LSP](https://github.com/swiftlang/sourcekit-lsp) so Claude Code gets code intelligence (go-to-definition, references, hover info):

```bash
brew install sourcekit-lsp
```

Then add it to your Claude Code settings (`~/.claude/settings.json`):

```json
{
  "lsp": {
    "swift": {
      "command": "sourcekit-lsp"
    }
  }
}
```

### 3. Run setup

Open Claude Code in your project directory and run:

```
/setup-config
```

This will:
- Ask for your app name, bundle ID, scheme, and description
- Let you pick features (SharePlay, Hand Tracking, Image Tracking, ECS)
- Replace all template placeholders with your project values
- Remove config blocks for features you don't use
- Generate starter spec files in `specs/`

### 4. Start building

After setup, all rules and skills are active. Try:

| Command | What it does |
|---------|-------------|
| `/review-h` | Code review with report card |
| `/debug-h` | Guided debugging for visionOS issues |
| `/debug-entity` | Inspect RealityKit entity hierarchy |
| `/immersive-debug` | Debug immersive space problems |
| `/find-docs` | Search Apple Developer Documentation |
| `/new-test` | Generate unit/E2E tests |
| `/spec` | Document completed work |

## Skills

Slash commands you can invoke during a Claude Code session.

### `/setup-config`
Interactive setup wizard. Collects your app name, bundle ID, and feature selections, then configures all template files for your project. Run this first.

### `/review-h`
Scans your Swift files across 9 categories (state management, concurrency, error handling, visionOS patterns, SharePlay, ECS, etc.) and produces a report card with severity-rated findings. Supports `fix N` to apply fixes inline.

### `/debug-h`
Structured debugging mentor. Walks you through an 8-step process: triage, locate, describe, check docs, add debug prints, diagnose, fix. Keeps changes minimal (max 10 lines per fix) and cleans up debug prints when done.

### `/debug-entity`
Inspects a RealityKit entity by name — shows its components, position, scale, children, and flags missing collision or hover setup. Run without arguments to list all entities in the project.

### `/immersive-debug`
Focused debugger for immersive space issues: hand tracking, collisions, entity visibility, attachments, image tracking, and space lifecycle. CLI-style flow with targeted diagnostic checks.

### `/find-docs`
Searches Apple Developer Documentation via MCP. Returns API summaries, platform availability, code examples, and relevant WWDC sessions. Supports shortcuts like `/find-docs rk` for RealityKit.

### `/new-test`
Generates unit tests and E2E tests for work completed in the current session. Uses `test_what_condition_expected` naming. Supports `unit`, `e2e`, or feature-specific arguments.

### `/spec`
Documents recently completed work as markdown specs in `specs/`. Auto-detects whether the work is an immersive experience, feature, or architecture change and files it accordingly.

## Agent

### `swiftui-visionos`
A specialized subagent Claude can delegate to for SwiftUI and visionOS tasks. Covers RealityKit/ECS, ARKit hand and image tracking, SharePlay, Swift 6 concurrency, and immersive space patterns. Loaded with the SwiftUI Expert plugin for modern API guidance. Claude routes complex implementation work here automatically.

## Features (toggleable)

The template uses conditional blocks (`<!-- IF:FEATURE -->`) so unused features are cleanly removed during setup:

- **SharePlay** — GroupActivities, spatial personas, message sync
- **Hand Tracking** — ARKit hand skeleton, gesture detection
- **Image Tracking** — Reference image anchors
- **ECS** — Custom RealityKit Components and Systems

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- Xcode with visionOS SDK
