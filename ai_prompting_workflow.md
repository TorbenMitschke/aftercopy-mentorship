# macOS Clipboard Manager (MVP) — Working Notes

Target: **macOS 15.5 (24F74)**  
MVP scope: **menu-bar**, **text-only**, **in-memory**, **N = 50**, ignore items
with **trimmed length < 4**.

---

## AI Prompting Workflow (repeatable)

### 1) Before coding a commit
Use this to prevent the AI from “autocoding” and to keep you in control.

**Template**
- Here’s my goal + acceptance check.
- Propose file structure + pseudocode only.
- List 3 likely pitfalls.

**Prompt**
> Here’s my goal + acceptance check:  
> Goal: (one sentence)  
> Done when: (observable behavior)  
> Constraints: Swift + AppKit, menu-bar app, text-only, in-memory only.  
>  
> Propose a file structure and pseudocode only (no full implementations).  
> List 3 likely pitfalls and how to avoid them.

---

### 2) After you wrote code (PR-style review)
Use this to get “production-grade” feedback without giving away authorship.

**Prompt**
> PR review this diff/snippet. Focus on: AppKit lifecycle, timers, thread safety,
> NSPasteboard edge cases, and future extensibility.  
>  
> Please:  
> 1) Identify correctness risks and race conditions  
> 2) Identify design smells (coupling, unclear boundaries)  
> 3) Suggest *small* diffs only (no rewrites)  
>  
> Code / diff:  
> ```  
> (paste here)  
> ```

---

### 3) When stuck (debugging without thrashing)
Use this to get a prioritized hypothesis list + smallest experiments.

**Prompt**
> I see behavior X but expected Y.  
>  
> Environment: macOS 15.5, Swift + AppKit.  
> Expected: …  
> Actual: …  
> Steps to reproduce: …  
> Logs: …  
> Relevant snippet: …  
>  
> Give me:  
> - A hypothesis list (most likely first)  
> - The smallest experiment to validate each  
> - The likely fix if the hypothesis is confirmed

---

## Project Decisions (lock-in for MVP)
- **OS:** macOS 15.5 (24F74)
- **History size:** 50 items (in-memory)
- **Capture rule:** store only if `text.trimmingCharacters(in: .whitespacesAndNewlines).count >= 4`
- **Clipboard detection:** polling `NSPasteboard.general.changeCount` using a `Timer`
- **UI:** menu bar only (`NSStatusItem`), minimal menu initially (counter + quit)
- **Non-goals for MVP:** persistence, images/files/RTF, sync, encryption, global hotkeys

---

# Git + GitHub: Rules of the Road (habits & best practices)

This section is meant to be “permanent” guidance you can keep reusing.

## Core principles
1) **Small commits, each runnable**  
   Every commit should build and (ideally) be testable. This makes rollback and
   review easy.

2) **Commit message describes intent**  
   Prefer: “Add HistoryStore with capacity + trimmed-length filter”  
   Avoid: “updates”, “wip”, “fix stuff”.

3) **Keep `main` green**  
   Don’t push broken code to `main`. Use feature branches and PRs (even if it’s
   just you).

4) **One change per commit**  
   Don’t mix refactors with behavior changes unless necessary. This improves
   reviewability.

5) **Never commit secrets**  
   No API keys, tokens, or personal data. If you later add settings files, keep
   them out of git if they contain secrets.

---

## Suggested repository setup (macOS/Xcode friendly)

### `.gitignore`
Use a Swift/Xcode `.gitignore` (includes `DerivedData/`, build outputs, etc.).
If you create the repo on GitHub, you can pick “Swift” as the template.

Sanity check: **DerivedData should never be tracked**.

### README baseline
Include:
- What the app does (MVP scope)
- How to build/run (Xcode version, steps)
- Known limitations
- Roadmap (iterations)

### License
Even if it’s “just for you,” add a license if you put it on GitHub. If you
don’t know which: pick **MIT** for permissive.

---

## Branching model (simple and effective)
- `main`: always runnable
- `feat/<short-topic>`: feature branches (e.g., `feat/status-item`, `feat/pasteboard-polling`)
- Optional: `chore/<topic>` for tooling/docs

### Typical flow
1) Create branch from `main`
  - `git checkout -b feat/<short-topic>`
2) Make small commits
  - `git add .`
  - `git status`
  - `git commit -m "Add small feature doing this"`
  - `git push origin feat/<short-topic>` or `git push -u origin feat/<short-topic>` (for more changes on that branch which will repeatedly be pushed to remote)
3) Open a PR back into `main`
4) Merge when checks pass and you’re satisfied
5) Pull main locally and delete feature branch on GitHub
  - `git checkout main | git fetch | git status | git pull`

Even solo, PRs give you:
- a place to write decisions (description)
- a review checklist
- a history of why you changed something

---

## Commit message guidelines (practical)
Use imperative mood (“Add”, “Fix”, “Refactor”):

- `Add status bar menu with Quit`
- `Add ClipboardSource polling changeCount`
- `Add HistoryStore capacity + dedupe + trim filter`
- `Refactor coordinator wiring for testability`
- `Fix timer lifecycle on app termination`

Optional (more structured):
- `feat: add ClipboardSource polling`
- `fix: prevent storing short/whitespace-only items`
- `chore: add README and license`

---

## PR (pull request) checklist (copy/paste)
Before merging to `main`, confirm:
- [ ] App launches and shows status item
- [ ] Clipboard capture works for text >= 4 trimmed chars
- [ ] Adjacent dedupe works (if implemented)
- [ ] History never exceeds 50 items
- [ ] No UI work off the main thread (AppKit requirement)
- [ ] No tight coupling (ClipboardSource doesn’t know UI details)
- [ ] Logs are helpful but not spammy
- [ ] No generated/build artifacts committed

---

## Tagging and releases (lightweight)
When you reach meaningful milestones, tag them:

- `v0.1.0` — menu bar + capture counter
- `v0.2.0` — show history items in menu
- `v0.3.0` — policies (dedupe, max size)
- `v0.4.0` — persistence (if/when)

Tags help you bisect regressions and communicate progress.

---

## Operational habits (local development)
- **Keep notes in the repo**: `docs/decisions.md` for small architecture decisions
- **Prefer reproducible steps**: document “how to run” and “how to debug”
- **Use issues** (even if solo): one issue per feature/bug, link PRs to issues

---

## Git CLI quickstart (minimal commands)
Initialize (if not created via Xcode/GitHub):
```bash
git init
git add .
git commit -m "Initial commit"
