---
name: workspace-management
description: Create and manage workspace entries for tasks and investigations. Use when starting a non-trivial task, saving findings, or resuming work from a previous session.
---

# Workspace Management Skill

Workspaces are persistent folders for notes, scripts, analysis, and artifacts produced during a task or investigation.

## Directory Structure

```
workspace/YYYY-MM-DD-task-name/
└── context.md       # Workspace index / menu: goal, current status, key docs, open items
└── ...other files   # Detailed findings, scripts, logs, analysis output, draft docs, etc.
```

**Base directory:** `<project-root>/workspace/` (always relative to the current project root)

```bash
# Run from project root
WORKSPACE_DIR="$(pwd)/workspace"
mkdir -p "$WORKSPACE_DIR"
```

---

## Creating a Workspace

When starting a new task or investigation:

1. Pick a short, descriptive name: `YYYY-MM-DD-<kebab-case-topic>` (use current date)
2. Create the folder and `context.md`
3. Write an initial `context.md` entry capturing:
   - the goal
   - current status
   - key documents in the workspace
   - open questions / next steps

---

## Using a Workspace

`context.md` is a workspace index. Update it **throughout the task** as findings emerge. Use it to:

- Summarize discoveries at a high level
- Link to detailed notes and artifacts
- Note decisions made and why
- Track current status, open questions, and next steps

Do not keep all detailed findings in `context.md`. Put substantial investigation details into separate focused files that live alongside `context.md`, for example:

- `current-state.md`
- `system-flow.md`
- `gap-analysis.md`
- `operational-notes.md`
- `technical-plan.md`
- `queries.sql`
- `logs.txt`

Other files (scripts, logs, analysis output, detailed notes, anything you need when working on the task) live alongside `context.md` in the same folder.

---

## Resuming a Workspace (New Session)

When the user resumes work on a topic or task:

1. **Check** if a relevant workspace already exists:

   ```bash
   # List all workspace folders (preferred)
   fd --type d --max-depth 1 --no-ignore-parent . workspace/

   # Filter by date prefix or topic keyword
   fd --type d --max-depth 1 --no-ignore-parent '2026-04' workspace/

   # Fallback if fd is unavailable
   ls workspace/ | grep '2026-04'
   ```

   > **Note:** Do NOT use `Glob: workspace/**` — Glob matches files only (not directories) and truncates results at ~100 entries.

2. **If found** — read `context.md` first before doing anything else
3. **Use `context.md` to identify which detailed files matter**, then open only the relevant documents
4. **Brief the user** on prior findings: "I found a previous session on this — here's what we covered: ..."
5. Continue from where things left off

---

## Rules

- Never create workspace files in the root directory or in `codebases/`
- One workspace per topic — don't create duplicates; reuse and append
- Keep `context.md` as the workspace index and navigation file
- Keep detailed findings in separate focused documents, not in `context.md`
