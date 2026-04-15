Perform a Claude Get Lean Protocol 1 (GLP1) audit for the current working directory. This is an interactive, multi-phase skill.

---

## Phase 0 — Welcome & Orientation

Before beginning any analysis, greet the user and explain what is about to happen. Use a friendly, professional tone — warm but efficient, like an HR onboarding coordinator walking a new employee through their first day.

Display the following (adapt the wording naturally, do not copy verbatim):

---

**Welcome to ClaudeGLP1 — your Claude Get Lean Protocol 1 audit.**

We're glad you're here. Over the next few phases, we'll be taking a close look at how your Claude environment is currently structured, identifying opportunities to streamline your setup, and making targeted improvements — all with your approval at every step.

Here's what we'll be doing together today:

- **Phase 1 — Discovery:** We'll take stock of everything currently loaded into your Claude sessions: configuration files, skills, plugins, hooks, and memory files.
- **Phase 2 — Token Report:** We'll put a number on it. You'll see exactly how much of your context window is being consumed before you even say hello.
- **Phase 3 — Recommendations:** We'll flag what's working, what's redundant, and what might be costing you more than it's worth.
- **Phase 4 — Backup:** Before we touch a single file, we'll create a complete snapshot of your current setup. Nothing is permanent here — you can always go back.
- **Phase 5 — Cleanup:** We'll walk through each recommendation together. You decide what stays, what goes, and what moves. No surprises.
- **Phase 6 — New Structure:** We'll show you your refreshed configuration as a clean hierarchy — a picture of your leaner, more intentional setup.
- **Phase 7 — Before & After:** A side-by-side comparison so you can see exactly what changed and what you gained.
- **Phase 8 — Restore (if needed):** If at any point you'd like to roll everything back, just run `/ClaudeGLP1 --restore` and we'll take you right back to where you started.

This process is collaborative and non-destructive. We won't make any changes without your explicit go-ahead.

When you're ready, just say the word and we'll get started with Phase 1.

---

Wait for the user to confirm before proceeding to Phase 1.

---

## Phase 1 — Discovery

Collect in parallel:

1. All CLAUDE.md files: `~/.claude/CLAUDE.md` + cwd and subdirs. Path, char count, one-line summary.
2. All files in `~/.claude/commands/` and `~/.claude/skills/` (if exists). Filename, char count.
3. MCP servers, plugins, extensions from `~/.claude/settings.json` and any project settings files.
4. All configured hooks. Flag any that inject output into the conversation (per-turn token cost).
5. `memory/recent-memory.md`, `memory/long-term-memory.md`, `memory/project-memory.md` relative to cwd. Char count each.

---

## Phase 2 — Initial Token Usage Report

Present a formatted table estimating token cost of each component loaded at session start. Use ~4 chars per token as the conversion. Include:

| Component | File/Source | Chars | Est. Tokens |
|---|---|---|---|
| Global CLAUDE.md | ~/.claude/CLAUDE.md | ... | ... |
| Vault/Project CLAUDE.md | ... | ... | ... |
| Deferred tools list | system-injected | measure | measure |
| Skills list (system-reminder) | system-injected | measure | measure |
| Memory: recent | memory/recent-memory.md | ... | ... |
| Hook outputs (per turn) | hooks | ... | ... |
| currentDate injection | system-injected | ~100 | ~25 |
| **TOTAL** | | | |

Then show: **"This session opened at approximately X tokens (~Y% of a 200k context window)"**

---

## Phase 3 — Recommendations

Analyze what was found and produce a prioritized list of recommendations, grouped by impact. For each item, show estimated token savings. Focus on: unused or redundant content, placeholder/boilerplate, misplaced rules, per-turn hook verbosity, and inactive MCP servers or plugins.

---

## Phase 4 — Backup

Before making any changes, create a timestamped backup of the current structure so it can be fully restored later.

1. Generate a timestamp in the format `YYYYMMDD_HHMMSS`.
2. Create the backup directory at `~/.claude/backups/glp1_<timestamp>/`.
3. Copy the following into that directory, preserving relative paths:
   - All CLAUDE.md files found in Phase 1 (maintain their full path structure under the backup root)
   - All files in `~/.claude/commands/`
   - `~/.claude/settings.json`
   - Any project-level `settings.json` files found
   - All memory files found in Phase 1

4. Write a manifest file at `~/.claude/backups/glp1_<timestamp>/MANIFEST.md` containing:
   - Timestamp
   - List of every file backed up with its original absolute path
   - The token totals from Phase 2 (pre-cleanup baseline)

5. Confirm to the user: **"Backup created at ~/.claude/backups/glp1_<timestamp>/. You can restore at any time with `/ClaudeGLP1 --restore glp1_<timestamp>`."**

Do not proceed to Phase 5 until the backup is confirmed complete.

---

## Phase 5 — Interactive Cleanup

For each item flagged in Phase 3, ask the user whether to:
- **Remove** it (delete the file or section)
- **Move** it (relocate to correct level)
- **Keep** it (skip)

Do not make any changes without explicit user confirmation per item. After the user responds to each prompt, make the approved changes immediately before moving to the next item.

---

## Phase 6 — New Folder Hierarchy

After cleanup, display the updated CLAUDE.md hierarchy as an org chart using plain text tree format:

```
~/.claude/CLAUDE.md           (global)
└── <cwd-parent>/CLAUDE.md         (intermediate, if present)
    └── <cwd>/CLAUDE.md            (project)
```

For each node show: path, size in chars, and a one-line summary of scope.

---

## Phase 7 — Before/After Report

Using the Phase 2 baseline, re-measure each component after cleanup and produce a comparison table (Before / After / Saved) for: CLAUDE.md files, skills/commands, memory files, hook output per turn, session open total, and per-turn overhead.

Follow with 3–5 bullet observations on what changed and why it matters for long sessions.

---

## Phase 8 — Restore (on-demand)

This phase is only triggered when the user runs `/ClaudeGLP1 --restore <backup-id>` or explicitly asks to roll back changes.

1. Locate the backup at `~/.claude/backups/<backup-id>/`. If not found, list available backups from `~/.claude/backups/` and ask the user to choose one.
2. Read `MANIFEST.md` from the backup and display:
   - When the backup was taken
   - How many files it contains
   - The pre-cleanup token baseline
3. Ask the user to confirm: **"This will overwrite current files with the backed-up versions. Proceed?"**
4. On confirmation, restore every file listed in the manifest to its original absolute path, overwriting current versions.
5. Report each file restored. Flag any files that were in the backup but whose destination path no longer exists (e.g. a directory was deleted) — create the parent directory and restore anyway.
6. Confirm: **"Restore complete. Your configuration has been rolled back to the state from <timestamp>."**

Do not delete the backup after restore — keep it available for re-use.
