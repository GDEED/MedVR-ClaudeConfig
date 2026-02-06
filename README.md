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
git clone https://github.com/YOUR_ORG/MedVR-ClaudeConfig.git .claude-template
cp -r .claude-template/.claude .
cp .claude-template/CLAUDE.md .
cp .claude-template/.mcp.json .
cp -r .claude-template/specs .
rm -rf .claude-template
```

Or copy the files manually — you just need `CLAUDE.md`, `.mcp.json`, `.claude/`, and `specs/` at your project root.

### 2. Run setup

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

### 3. Start building

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

## Features (toggleable)

The template uses conditional blocks (`<!-- IF:FEATURE -->`) so unused features are cleanly removed during setup:

- **SharePlay** — GroupActivities, spatial personas, message sync
- **Hand Tracking** — ARKit hand skeleton, gesture detection
- **Image Tracking** — Reference image anchors
- **ECS** — Custom RealityKit Components and Systems

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- Xcode with visionOS SDK
