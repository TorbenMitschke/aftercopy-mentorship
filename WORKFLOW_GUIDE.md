# aftercopy — Workflow Guide

This document describes how to use Claude Projects effectively as your engineering mentor. It lives **only** in Claude's Project Knowledge — not in the git repo. See "File Location Strategy" below for why.

---

## The Core Loop

```
┌─────────────────────────────────────────────┐
│  1. LEARN     — Open a focused conversation  │
│                  about the next concept/task  │
│                                               │
│  2. BUILD     — Write code in Xcode           │
│                                               │
│  3. UPLOAD    — Update .swift files in        │
│                  Project Knowledge             │
│                                               │
│  4. REVIEW    — Start a code review           │
│                  conversation                  │
│                                               │
│  5. FIX       — Apply fixes in Xcode          │
│                                               │
│  6. COMMIT    — git add → commit → PR → merge │
│                                               │
│  7. UPDATE    — PROJECT_STATE.md (Project  │
│                  Knowledge) + ARCHITECTURE.md │
│                  (repo, if changed)            │
│                                               │
│  └──────────── Loop back to step 1 ──────────┘
```
### **Detailed Branch → PR → Merge Flow**
```
1. BRANCH:   git checkout -b feat/your-feature
2. CODE:     Write implementation in Xcode
3. REVIEW:   Upload to Project Knowledge → new chat for review
4. FIX:      Apply fixes from review in Xcode
5. COMMIT:   git add → git commit -m "feat: ..." → git push -u
6. PR:       Create pull request on GitHub (DO NOT merge yet)
7. VERIFY:   Review PR diff, optionally ask Claude for final check
8. MERGE:    Merge PR on GitHub if all checks pass
9. CLEANUP:  git checkout main → git pull → git branch -d feat/...
```
### When to create a PR:

- After completing and committing a logical, testable unit of work
- Prefer smaller PRs (1-3 commits) while learning

### When to merge a PR:

- Code review complete, all issues resolved
- App runs without crashes or warnings
- You can explain every line of code committed
- All acceptance criteria met (if defined)

---

## Conversation Strategy

Start **separate conversations** for separate concerns. Don't let a single chat become a sprawling thread that loses focus.

**Examples of good conversation scoping:**
- "Iteration 1, Commit 1: AppDelegate + StatusItem setup"
- "Code review: ClipboardMonitor.swift"
- "Concept deep-dive: how does NSPasteboard polling work?"
- "Architecture decision: how should I structure the data model?"
- "Debugging: Timer not firing after app launch"

Each conversation inherits the system prompt and can access all Project Knowledge files.

---

## Writing Acceptance Criteria

Every GitHub Issue needs clear acceptance criteria before you start implementing. Use this template and framework to write bulletproof criteria.

### The Template

Copy this into every issue and fill in the feature-specific sections:
```markdown
## Acceptance Criteria

### Functional Requirements
- [ ] [Core behavior works as specified - be specific about inputs/outputs]
- [ ] [Edge case 1 handled correctly]
- [ ] [Edge case 2 handled correctly]

### Code Quality
- [ ] No compiler warnings (or warnings are understood and documented)
- [ ] No force-unwraps unless intentional and justified
- [ ] Swift naming conventions followed
- [ ] Xcode build succeeds without errors

### Integration
- [ ] Feature integrates with existing code without breaking functionality
- [ ] App launches and runs without crashes
- [ ] [Specific interaction with other component works, if applicable]

### Understanding (Learning Requirement)
- [ ] Can explain every line of code written for this feature
- [ ] Can explain why this approach was chosen over alternatives
- [ ] Can identify at least one limitation or edge case of this approach

### Documentation
- [ ] ARCHITECTURE.md updated if design decisions were made
- [ ] PROJECT_STATE.md updated with what was learned
- [ ] Code comments added for non-obvious logic (if any)

### Testing (Manual for MVP)
- [ ] Tested on clean build (Cmd+Shift+K → Cmd+B → Cmd+R)
- [ ] Tested after relaunching the app
- [ ] [Feature-specific test scenario completed]
```

### The 5-Question Framework

Before writing criteria, answer these questions:

1. **What is the happy path?** → Functional requirements
   - "When everything goes right, what should happen?"

2. **What can go wrong?** → Edge cases
   - "What edge cases or error conditions exist?"

3. **How do I know it's working?** → Testing
   - "What can I see/test to verify this?"

4. **What did I learn?** → Understanding
   - "What concept does this feature teach me?"

5. **How does this connect?** → Integration
   - "What dependencies or interactions exist?"

### Example: Issue #2 "Replace status item text with icon"
```markdown
## Acceptance Criteria

### Functional Requirements
- [ ] Status item displays a custom icon (not text)
- [ ] Icon is loaded from Assets.xcassets
- [ ] Icon is properly sized for menu bar (20x20 @2x)
- [ ] Icon renders as template image (adapts to light/dark mode)

### Code Quality
- [ ] No compiler warnings
- [ ] Icon asset properly configured in Assets.xcassets
- [ ] Code uses `button.image` property (not `button.title`)

### Integration
- [ ] Removing text title doesn't break status item rendering
- [ ] App still launches and shows status item immediately

### Understanding
- [ ] Can explain difference between `button.title` and `button.image`
- [ ] Can explain what "template image" means for menu bar icons
- [ ] Can explain how Assets.xcassets files are bundled into the app

### Documentation
- [ ] ARCHITECTURE.md updated if image loading strategy is significant
- [ ] PROJECT_STATE.md updated with NSImage/template image learnings

### Testing
- [ ] Tested on clean build
- [ ] Icon appears immediately on app launch
- [ ] Icon visible in both light and dark menu bar appearances
```

### Quick Criteria Quality Check

Before starting implementation, verify:
- [ ] Each criterion is **testable** (can verify in <1 minute)
- [ ] Criteria are **specific** (not vague like "works well")
- [ ] **Edge cases** are identified
- [ ] **Understanding** requirements are included
- [ ] You'll know **exactly when you're done**

### Red Flags (Bad Criteria)

Avoid these patterns:
- ❌ "Code is clean" → Too vague
- ❌ "Feature works" → Not specific
- ❌ "Use the right API" → Doesn't say which API
- ❌ "Make it look good" → Not testable
- ❌ "Implement X correctly" → Circular definition

Good criteria are **concrete, testable, and specific**.

---

## Code Review Checkpoints

### When to trigger a review
- After every 2-3 commits (don't let too much code accumulate unreviewed)
- Before merging a feature branch to main
- When something feels "off" but you can't pinpoint why
- When you've just learned a new concept and applied it — verify you applied it correctly

### How to request a review
Upload your current `.swift` files to Project Knowledge (replacing old versions), then start a new conversation:

> "Review the current codebase. Don't change anything. For each issue:
> 1. What you observed
> 2. Why it matters
> 3. A question that leads me toward the fix
>
> Rank by severity. At the end, tell me what I'm doing well."

### After the review
1. Work through each finding in the conversation — discuss, don't just accept
2. Go back to Xcode and make the fixes yourself
3. If a fix taught you something new, add it to PROJECT_STATE.md under "What I've Learned"

---

## File Location Strategy

Your project has two categories of files that serve different purposes and live in different places.

### In the public git repo (aftercopy)

These files are part of the software project itself. They're legitimate documentation that adds portfolio value.

| File | Purpose |
|------|---------|
| `README.md` | Standard project readme |
| `ARCHITECTURE.md` | Component design and rationale — shows engineering thinking |
| `PARKING_LOT.md` | Deferred features — shows disciplined scoping |
| `*.swift` source files | The actual codebase |

### Claude Project Knowledge only (NOT in the repo)

These files are learning scaffolding — they exist to make Claude an effective mentor. They don't belong in the public repo for two reasons:

1. **Security.** Public prompt/workflow files can be modified via PRs, potentially injecting instructions that alter Claude's behavior. The system prompt itself lives in Claude's Project Instructions (the settings field), but supporting docs still influence context.
2. **Distribution.** Users of aftercopy don't need your mentorship workflow. Shipping these files with the software makes no sense.

| File | Purpose | Update frequency |
|------|---------|-----------------|
| SYSTEM_PROMPT.md | Reference copy of the project instructions | Rarely (only if you change the prompt) |
| PROJECT_STATE.md | Current progress, decisions, learnings | After every session or milestone |
| WORKFLOW_GUIDE.md | This file — process reminders | Rarely |
| Claude_prompting_guide.md | Prompting reference material | Rarely |

### Where to store mentorship files locally

Keep a local folder outside the repo for your mentorship files:

```
~/Documents/aftercopy-Mentorship/
├── SYSTEM_PROMPT.md
├── PROJECT_STATE.md
├── WORKFLOW_GUIDE.md
└── Claude_prompting_guide.md
```

Optionally, track these in a **separate private repo** (e.g., `aftercopy-mentorship`) if you want version history on your learning journey. This is also a portfolio asset — a private one you can selectively share in interviews to demonstrate how you learn.

### Source files live in both places

Your `.swift` files are tracked in the public repo AND uploaded to Project Knowledge for Claude to review. These are the only files that need to exist in both locations. Always re-upload after making changes in Xcode.

### The #1 mistake to avoid
**Forgetting to re-upload source files after making changes in Xcode.** If your Project Knowledge has stale code, Claude reviews the wrong version and gives misleading advice. Before any code review conversation, always ask yourself: "Are these files current?"

---

## Prompt Templates

Copy-paste these into new conversations as needed.

### Starting a new task
```
I'm starting on [task description]. Before I write any code:
1. What concepts do I need to understand?
2. What are the key design decisions I should think about?
3. Ask me questions to check my understanding before I proceed.
```

### When stuck (graduated hints)
```
I'm stuck on [problem description]. Give me a Level 1 hint
(conceptual direction only). I'll ask for more if needed.
```

### Code review
```
Review the uploaded files. Don't rewrite anything. For each issue:
(1) what you observed, (2) why it matters, (3) a question that
leads me toward the fix. Rank by severity.
```

### After completing a commit
```
I just committed [description]. Before I move on:
1. Did I miss anything in the acceptance criteria?
2. Is there anything I should clean up?
3. Quiz me — ask me to explain a concept from what I just built.
```

### End of session check-in
```
I'm wrapping up for today. Help me update PROJECT_STATE.md:
1. What did I accomplish?
2. What did I learn?
3. What's my next step when I pick this up again?
4. Does ARCHITECTURE.md in the repo need updating?
```

---

## Definition of Done — Checklist (per iteration)

Before marking any iteration as complete, verify:

- [ ] All acceptance criteria for every commit in the iteration are met
- [ ] App runs without crashes from Xcode
- [ ] No Xcode warnings (or all warnings documented and understood)
- [ ] Code reviewed by Claude with all critical/important issues resolved
- [ ] **I can explain every line of code I committed**
- [ ] PROJECT_STATE.md is updated (in Project Knowledge + local mentorship folder)
- [ ] ARCHITECTURE.md reflects any new decisions (in the repo — commit it)
- [ ] All branches merged and deleted
