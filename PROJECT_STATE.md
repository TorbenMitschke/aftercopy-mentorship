# aftercopy — Project State

> **Last updated:** [DATE — update this after each session]

## Current Iteration

**Iteration 1:** Menu bar presence + clipboard capture + basic display

### Features
- [x] #1: Basic status item with text "aftercopy"
- [x] #2: Replace text with icon
- [ ] #3: Add menu to status item
- [ ] #4: Implement Clipboard pulling
- [ ] #5: Display "Captured: N" in menu
---

## Moving From One Feature to Another

### The Checklist:

Before starting a new feature, verify the previous one is **fully done**:
```
Previous Feature Completion Checklist:
✅ Code merged to main
✅ Branch deleted locally and on GitHub
✅ GitHub Issue closed
✅ PR closed
✅ PROJECT_STATE.md updated (moved to "What I've Learned")
✅ You can explain every line of code
✅ App runs without issues
```
Then start the next feature:
```
New Feature Startup Checklist:
✅ GitHub Issue created with acceptance criteria
✅ PROJECT_STATE.md updated with "Next Step"
✅ Concept chat opened (or continued) for learning
✅ On main branch with latest changes: git checkout main, git pull
```
### Current Focus
Issue #2 - Learning how NSImage works for status bar icons

## Current Status

- [x] Repository initialized
- [x] Git workflow established (feature branches, single-purpose commits, PRs, branch cleanup)
- [x] PARKING_LOT.md created and committed (chore/parking-lot branch, merged to main)
- [x] Xcode project created with template for macOS app
- [x] First retrieval of system clipboard with NSPasteboard.general and .string(forType: .string)
- [x] AppDelegate + NSStatusItem setup
- [x] Polish NSStatusItem with icon
- [x] Removed window and menu from Xib
- [ ] Make NSStatusItem interactive
- [ ] Capture system clipboard
- [ ] XIB cleanup (window and main menu)
- [ ] Timer-based clipboard polling
- [ ] Privacy filter (< 4 chars ignored)
- [ ] Deduplication logic
- [ ] "Captured: N" display in menu bar
- [ ] Clean quit behavior

## Architecture Decisions Made

| Decision | Rationale | Date |
|----------|-----------|------|
| AppDelegate-based lifecycle (not SwiftUI @main) | Explicit control over NSApplication lifecycle; better for learning macOS fundamentals | Session 1 |
| NSStatusItem for menu bar | Standard macOS API for menu bar apps | Session 1 |
| Timer-based NSPasteboard polling | Simpler than notification-based approach for MVP; revisit later if needed | Session 1 |
| Privacy filter: ignore trimmed length < 4 | Avoid capturing single chars, passwords fragments, accidental copies | Session 1 |

## What I've Learned So Far

> Fill this in after each session — in your own words. This reinforces learning and gives Claude context.

- Git workflow: branching (`git checkout -b`), staging specific files (`git add <file>`), `-u` flag for push, PR creation, branch deletion after merge.
- Where print statments need to be located to be shown in the console after the start up of the application
- Accessed system clipboard and validated it with print statement
- How to change the menu bar (top right) to display a custom string
- Context of the status item being outside of launch function
- How to display an icon in the menu bar
- How to use Apple's SF Symbols (loaded via `systemSymbolName:`)
- What image as template means
- Understood the connection from Xib to code and how to safely remove them without any warnings
- Learned the differences between apps activation policies (.regular, .accessory and .prohibited), how and when to change them
- Touched build configurations -> to change behavior before first function is run
- [Add more as you learn]

## Open Questions / Things I Don't Fully Understand Yet

> Be honest here. Claude will use this to calibrate guidance.

- Swift 6 concurrency implications (strict `@Sendable`, `@MainActor` checking) — need to confirm Xcode version first.
- How NSPasteboard.general actual works in combination with .string(forType: .string).
- How exactly the evaluation of ("The clipboard has: \(content ?? "nothing")") works.
- How NSPasteboard.changeCount actually works under the hood.
- How to correctly control properties of .button in detail
- [Add more as they come up]

## Next Steps

- Menu on click
- Customize icons
