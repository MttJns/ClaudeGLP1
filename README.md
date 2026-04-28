# ClaudeGLP1 — Claude Get Lean Protocol 1

A 5 minute Claude Code skill that audits your Claude environment, estimates how much context window you're burning at session open, and guides you through an interactive cleanup — with a full backup and one-command restore.

<img width="701" height="561" alt="image" src="https://github.com/user-attachments/assets/99201b6c-bba3-456e-a28b-52074ef95cbc" />


---

## What it does

If you've been trying out agent tools like /GSD and others, your Claude is probably wasting tons of tokens. Each Claude Code session loads a surprising amount of content before you type anything: CLAUDE.md files, skills, hooks, memory files, MCP server configs, and system-injected tool lists. ClaudeGLP1 measures all of it, flags what's redundant or misplaced, and helps you trim it down — it takes about 5 minutes.

**The 8 phases:**

| Phase | Name | What happens |
|---|---|---|
| 0 | Welcome | Friendly orientation — explains what's coming and waits for your go-ahead |
| 1 | Discovery | Scans CLAUDE.md hierarchy, skills, hooks, plugins, and memory files |
| 2 | Token Report | Estimates tokens consumed at session open, per component |
| 3 | Recommendations | Flags quick wins and structural improvements with token savings |
| 4 | Backup | Snapshots your entire current setup before touching anything |
| 5 | Cleanup | Walks through each recommendation — you approve every change |
| 6 | New Hierarchy | Shows your refreshed CLAUDE.md structure as a plain-text org chart |
| 7 | Before/After | Side-by-side token comparison: before vs. after |
| 8 | Restore | On-demand rollback to any previous backup |

---

## Requirements

- [Claude Code](https://claude.ai/code) installed and authenticated
- macOS or Linux (Windows path adjustments may be needed)

---

## Installation

### From GitHub

```bash
# Clone the repo
git clone https://github.com/<username>/ClaudeGLP1.git
cd ClaudeGLP1

# Copy the skill into your Claude commands directory
cp ClaudeGLP1.md ~/.claude/commands/ClaudeGLP1.md
```

### Manual

Download `ClaudeGLP1.md` and copy it directly:

```bash
cp /path/to/ClaudeGLP1.md ~/.claude/commands/ClaudeGLP1.md
```

That's it. No dependencies, no configuration required. Claude Code will pick up the new skill automatically — no restart needed.

### Verify installation

Start a Claude Code session and run:

```
/ClaudeGLP1
```

If Phase 0 appears with the welcome message, you're set.

---

## Updating

```bash
cd ClaudeGLP1
git pull
cp ClaudeGLP1.md ~/.claude/commands/ClaudeGLP1.md
```

---

## Usage

Start a full audit:
```
/ClaudeGLP1
```

Restore a previous backup:
```
/ClaudeGLP1 --restore glp1_20260413_143022
```

List available backups (tell Claude to list them):
```
/ClaudeGLP1 --restore
```

---

## What gets backed up

Before any changes are made, ClaudeGLP1 saves:

- All `CLAUDE.md` files in your hierarchy (global, vault, project levels)
- All files in `~/.claude/commands/`
- `~/.claude/settings.json` and any project-level settings files
- Memory files (`recent-memory.md`, `long-term-memory.md`, `project-memory.md`)

Backups are stored at `~/.claude/backups/glp1_<timestamp>/` with a `MANIFEST.md` that records every file's original path and the pre-cleanup token baseline.

---

## What it looks for

**Quick wins:**
- Unused or redundant skills in `~/.claude/commands/`
- Placeholder content in CLAUDE.md files (stub tables, empty sections)
- Hooks with `"verbose": true` that inject output into every conversation turn
- Memory files that are empty or contain only boilerplate

**Structural issues:**
- Rules in a CLAUDE.md that belong at a different level in the hierarchy
- Content duplicated across global / vault / project levels
- MCP servers or plugins that are configured but not actively used

---

## CLAUDE.md hierarchy

Claude Code supports three levels of configuration, loaded from outermost to innermost:

```
~/.claude/CLAUDE.md              ← global (all projects)
└── ~/your-vault/CLAUDE.md       ← vault or workspace level
    └── ~/your-vault/project/CLAUDE.md   ← per-project
```

ClaudeGLP1 helps you put the right rules at the right level so nothing is duplicated and context overhead is minimized.

---

## Token estimation

ClaudeGLP1 uses ~4 characters per token as a conversion approximation. System-injected content (deferred tools list, skills reminder, date injection) is estimated from observed sizes and noted as approximate. Actual usage may vary slightly by model and session.

---

## Notes

- ClaudeGLP1 never makes changes without your explicit confirmation per item.
- Backups are never deleted automatically — they accumulate in `~/.claude/backups/` and can be restored at any time.
- The skill has no external dependencies and works entirely with your local filesystem.
- Tested with Claude Code CLI on macOS. Should work on Linux; Windows paths may need adjustment.
