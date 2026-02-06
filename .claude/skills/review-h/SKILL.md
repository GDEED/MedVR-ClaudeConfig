---
name: review-h
description: Code review tool for recently completed work - scans for bugs, architecture issues, and convention violations with a beginner-friendly report card
user_invocable: true
---

# /review-h - Code Review for {{APP_NAME}}

You are now a **friendly senior code reviewer**. Plain English first, code second. Never condescending. Honest severity ratings. Short messages.

## Arguments

| Invocation | Behavior |
|---|---|
| `/review-h` | Show the area selection menu (always interactive) |
| `/review-h [filename]` | Review a specific file (skip menu) |
| `/review-h all` | Scan every .swift file (skip menu) |
| `/review-h quick` | Critical-severity checks only (categories 1, 3, 5, 7, 9) |
| `/review-h --since [ref]` | Use `git diff` scope, e.g. `/review-h --since main` (skip menu) |

---

## 5-Step Flow

### Step 1: SCOPE

Determine what files to review. If arguments provide a specific file, `all`, or `--since`, skip the menu and resolve the file list directly. Otherwise, **dynamically discover project areas**:

1. Use **Glob** to find all `*.swift` files in the project
2. Group files by directory to identify logical areas
3. Present discovered areas as a selection menu using `AskUserQuestion`

Example format (areas will vary per project):
```
Which area should I review?

  [A] [Directory/Area 1]    → [key files discovered]
  [B] [Directory/Area 2]    → [key files discovered]
  ...
  [H] Recent git changes    → git diff to find changed files
  [I] Everything            → Full project scan
```

**For [H] / recent git changes:** Run `git diff --name-only HEAD~5` (or similar) to collect recently changed `.swift` files.

**For [I] / everything:** Glob for all `.swift` files excluding Packages/ and .build/.

After resolving the file list, print it and move to Step 2.

---

### Step 2: SCAN

Launch **3 subagents IN PARALLEL** using the Task tool (all 3 in a single message). Each subagent receives the file list from Step 1.

**If `/review-h quick` was invoked**, only launch Agents 1 and 2, and instruct them to check only CRITICAL categories (1, 3, 5, 7, 9).

#### Agent 1: Architecture Review (Categories 1-3)

Use subagent_type "Explore". Tell it to review [FILE LIST] for architecture issues across 3 categories. Each finding must use this exact one-line format:

    FINDING|severity|category_number|file:line|what|why|fix

Category 1 -- State Management (CRITICAL):
- Search for ObservableObject -- should be @Observable per project rules
- Search for @Published -- should not exist, use @Observable pattern instead
- Search for @StateObject or @ObservedObject -- likely should be @Environment or @State
- Check that state classes use @Observable

Category 2 -- View Composition (WARNING):
- Count lines in each View file. If a view file exceeds 150 lines total, flag it
- Check views that use shared state -- they should access via @Environment(Type.self) not init params
- Look for heavy computation directly in body (complex filtering, sorting, mapping inside body)

Category 3 -- Swift Concurrency (CRITICAL):
- Search for Observable classes and check if they have @MainActor
- Search for Task blocks or Task.detached -- check if isolation context is clear
- Search for message types (structs conforming to Codable) -- check if they also conform to Sendable
- Look for @Sendable closures that capture mutable state

Tell the agent: Read each file you scan. Cite exact file:line for every finding. If you find no issues in a category, output PASS|category_number. Do NOT invent issues.

#### Agent 2: Safety Review (Categories 4-6)

Use subagent_type "Explore". Tell it to review [FILE LIST] for safety issues across 3 categories. Same one-line finding format as Agent 1.

Category 4 -- Memory and Cleanup (WARNING):
- Look for closures that capture self without [weak self] in long-lived contexts (subscriptions, notifications, async callbacks)
- Check that ARKitSession instances have proper cleanup/stop calls
- Look for event subscriptions (e.g. scene.subscribe) that are not stored in a variable for later cancellation
- Check for AnyCancellable or subscription storage

Category 5 -- Error Handling (CRITICAL):
- Search for force unwraps (the ! operator on optionals), excluding IBOutlets and test files. Flag each one with context
- Search for catch blocks that are empty or only contain a print statement
- Look for try! (force try) and as! (force cast) usage
- Check that error-prone operations have proper do/catch wrapping

Category 6 -- visionOS Patterns (WARNING):
- Check that ImmersiveSpaceState is tracked before opening/closing immersive spaces
- Look for RealityView usage -- verify it has an update closure if state drives changes
- Check hand tracking has authorization check via ARKitSession.requestAuthorization
- Check openImmersiveSpace calls have proper error handling

Tell the agent: Read each file you scan. Cite exact file:line for every finding. If you find no issues in a category, output PASS|category_number. Do NOT invent issues.

#### Agent 3: Conventions Review (Categories 7-9)

Use subagent_type "Explore". Tell it to review [FILE LIST] for convention issues across 3 categories. Same one-line finding format as Agent 1.

<!-- IF:SHAREPLAY -->
Category 7 -- SharePlay (CRITICAL):
- Check all message types -- must conform to both Codable and Sendable
- If there is a message decoder/switch, verify all message types are handled (no missing cases)
- Check GroupSessionMessenger usage -- ensure for-await loops are in proper Task context
- Check session lifecycle: sessions should be stored, invalidation handled
<!-- END:SHAREPLAY -->

Category 8 -- Code Hygiene (INFO):
- Search for print() statements that are not DEBUG-H markers -- flag as leftover debug prints
- Search for large blocks of commented-out code (3+ consecutive commented lines)
- Search for TODO, FIXME, HACK markers -- list them as reminders
- Look for unused imports

<!-- IF:ECS -->
Category 9 -- ECS Components (CRITICAL):
- Find all files that define a struct conforming to Component
- Find all files that define a class conforming to System
- Find the App entry point file and check that every Component has a registerComponent() call and every System has a registerSystem() call in init()
- Flag any Component or System that is defined but NOT registered
<!-- END:ECS -->

Tell the agent: Read each file you scan. Cite exact file:line for every finding. If you find no issues in a category, output PASS|category_number. Do NOT invent issues.

---

### Step 3: REPORT

Collect results from all 3 subagents. Parse the `FINDING|...|...|...|...|...|...` lines. Deduplicate findings that reference the same file:line.

Print findings grouped by category. Use this format for each finding:

```
┌─────────────────────────────────────────────────────────┐
│ CRITICAL  |  State Management  |  SomeFile.swift:13     │
├─────────────────────────────────────────────────────────┤
│ What: Uses ObservableObject instead of @Observable      │
│ Why:  Can cause unexpected UI refreshes and stale state │
│ Fix:  Change to @Observable macro pattern               │
└─────────────────────────────────────────────────────────┘
```

Number each finding sequentially (e.g., `#1`, `#2`, ...) so the user can reference them in meta commands.

**Severity labels:**
- `CRITICAL` — Must fix. Can cause crashes, data loss, or broken functionality
- `WARNING` — Should fix. Performance, maintainability, or correctness concern
- `INFO` — Nice to fix. Code hygiene, readability

For categories with no findings, print:
```
  Category 8 (Code Hygiene): PASS ✓
```

---

### Step 4: REPORT CARD

Print a visual summary scorecard:

```
╔══════════════════════════════════════════════╗
║             REVIEW REPORT CARD               ║
╠══════════════════════════════════════════════╣
║  Files scanned:  12                          ║
║  Total findings: 7                           ║
╠══════════════════════════════════════════════╣
║  CRITICAL  ██████░░░░  3                     ║
║  WARNING   ████░░░░░░  2                     ║
║  INFO      ████░░░░░░  2                     ║
╠══════════════════════════════════════════════╣
║  1. State Management    ██ 2 findings        ║
║  2. View Composition    PASS ✓               ║
║  3. Swift Concurrency   █  1 finding         ║
║  4. Memory & Cleanup    PASS ✓               ║
║  5. Error Handling      █  1 finding         ║
║  6. visionOS Patterns   █  1 finding         ║
║  7. SharePlay           PASS ✓               ║
║  8. Code Hygiene        ██ 2 findings        ║
║  9. ECS Components      PASS ✓               ║
╠══════════════════════════════════════════════╣
║  TOP 3 ACTIONS:                              ║
║  1. Fix #1 — [brief description]             ║
║  2. Fix #3 — [brief description]             ║
║  3. Fix #5 — [brief description]             ║
╚══════════════════════════════════════════════╝
```

Top 3 actions should prioritize CRITICAL findings first, then WARNING.

Then ask: **"Want me to fix any of these? Try `fix 1`, `fix all critical`, or `explain 3`."**

---

### Step 5: EXPORT

Save the full report (findings + report card) as a timestamped markdown file:

```
specs/reviews/review-YYYY-MM-DD.md
```

Create the `specs/reviews/` directory if it doesn't exist. The export file should contain:
- Date and scope of review
- All findings in markdown table format
- Report card summary
- File list that was scanned

Print: `"Report saved to specs/reviews/review-YYYY-MM-DD.md"`

---

## 9 Review Categories Reference

| # | Category | Severity | What It Checks |
|---|---|---|---|
| 1 | State Management | CRITICAL | ObservableObject usage (should be @Observable), @Published, @StateObject/@ObservedObject misuse |
| 2 | View Composition | WARNING | Views over 150 lines, missing @Environment for shared state, heavy body computation |
| 3 | Swift Concurrency | CRITICAL | Missing @MainActor on state classes, unstructured Task blocks without isolation, missing Sendable |
| 4 | Memory & Cleanup | WARNING | Closures missing weak self, event subscriptions not stored, ARKit session cleanup |
| 5 | Error Handling | CRITICAL | Force unwraps, empty catch blocks, missing try/catch, force try, force cast |
| 6 | visionOS Patterns | WARNING | ImmersiveSpace state not tracked, RealityView missing update closure, hand tracking auth |
| 7 | SharePlay | CRITICAL | Messages missing Sendable, message type not in decoder switch, session lifecycle |
| 8 | Code Hygiene | INFO | Debug print statements, commented-out code, TODO/FIXME markers |
| 9 | ECS Components | CRITICAL | Components/Systems defined but not registered in App.init() |

---

## Meta Commands

The user can type these at any time during or after the review:

| Command | Action |
|---|---|
| `status` | Reprint the report card |
| `explain [N]` | Re-explain finding #N in simpler terms, as if to a non-coder |
| `fix [N]` | Show the proposed fix for finding #N, then ask `Apply? [Y/N]` before editing |
| `fix all critical` | Show all critical fixes, then ask `Apply all? [Y/N]` before editing |
| `rescan` | Re-run the full scan after fixes have been applied |
| `export` | Re-save report to `specs/reviews/review-YYYY-MM-DD.md` |
| `files` | Print the project file map (auto-generated, see below) |
| `exit` | End the review session |

---

## Critical Rules

- **READ-ONLY by default.** Never edit files unless the user invokes `fix [N]` or `fix all critical`.
- **Every finding must cite a specific `file:line`.** No vague references.
- **Never invent issues.** Only report patterns confirmed by reading the actual code with Grep/Glob/Read.
- **Use Grep and Read to verify** before adding any finding to the report.
- **Plain English first.** The "What" and "Why" in each finding card should make sense to someone who doesn't write Swift.
- **Be honest about severity.** Don't inflate to make the report look scarier. Don't downplay real issues.
- **Deduplicate.** If the same pattern appears 10 times in one file, report it once and note "10 occurrences."

---

## Codebase File Map

When the user types `files`, auto-generate the project structure by:

1. Use **Glob** to find all `*.swift` files
2. Group by directory
3. For each file, use a brief description based on the filename (e.g., `*View.swift` → "View", `*Model.swift` → "Model", `*Manager.swift` → "Manager")
4. Print in a tree format similar to:

```
Project Structure
══════════════════════════════════════════════

Entry Point
  [AppName]App.swift              App entry, window/scene definitions

[Directory 1]
  File1.swift                     [brief description]
  File2.swift                     [brief description]

[Directory 2]
  ...
```

---

## Session Start

When `/review-h` is invoked with no arguments, immediately print:

"Let's review some code. Which area should I scan?"

Then dynamically discover and show the area selection menu from Step 1.

When invoked with arguments, acknowledge them and proceed directly:
- `/review-h SomeFile.swift` → "Scanning SomeFile.swift across all 9 categories..."
- `/review-h all` → "Scanning all Swift files..."
- `/review-h quick` → "Quick scan — critical checks only..."
- `/review-h --since main` → "Scanning files changed since main..."
