# master-plan

AI-native task management for [Claude Code](https://claude.com/claude-code). Track tasks in a `MASTER_PLAN.md` file, pick what to work on, save progress, and ship — all through agent skills.

```
/master-plan:next  →  pick a task  →  /master-plan:save  →  pause safely  →  /master-plan:done  →  ship it
```

## Why?

- **Zero infrastructure** — Just a markdown file + git. No Jira, no Linear, no database.
- **AI-native workflow** — Claude reads the plan, scores priorities, picks tasks, updates status, runs tests, commits. You just approve.
- **Session-resilient** — `/save` means you can close your laptop, switch machines, come back tomorrow, run `/next`, and you're right where you left off.
- **Self-documenting** — MASTER_PLAN.md IS the project history. No separate tracking tool to keep in sync.
- **Cross-platform** — Skills follow the [Agent Skills open standard](https://agentskills.io). Works in Claude Code, Cursor, GitHub Copilot, Windsurf, and more.

## Install

```
/plugin marketplace add endlessblink/master-plan
/plugin install master-plan
```

Or add to your project's `.claude/settings.json` for team auto-install:

```json
{
  "extraKnownMarketplaces": {
    "endlessblink-tools": {
      "source": { "source": "github", "repo": "endlessblink/master-plan" }
    }
  },
  "enabledPlugins": {
    "master-plan@endlessblink-tools": true
  }
}
```

## Skills

### `/master-plan:task` — Create a Task

Adds a new task to MASTER_PLAN.md with an auto-generated sequential ID.

```
> /master-plan:task

Using task number: 42

[Asks: Type? Priority? Title?]

Task added to MASTER_PLAN.md:
- ID: TASK-42
- Title: Add user authentication
- Priority: P1
- Status: PLANNED
```

Supports types: `TASK`, `BUG`, `FEATURE`, `INQUIRY`
Priorities: `P0` (critical) → `P3` (backlog)

### `/master-plan:next` — Pick What to Work On

Reads MASTER_PLAN.md, scores tasks by priority and status, presents an interactive picker.

```
> /master-plan:next

You have 2 tasks in progress.

[Interactive picker with top tasks sorted by priority]

> Selects TASK-42

## Selected Task: TASK-42
Title: Add user authentication
Priority: P1
Status: PLANNED
...

[Offers: Start working / Pick different / Just show context]
```

Filters: `/master-plan:next bugs`, `/master-plan:next planned`, `/master-plan:next active`

### `/master-plan:save` — Save Work-in-Progress

Commits and pushes without marking the task done. Perfect for switching machines or ending a session.

```
> /master-plan:save

[Shows changed files, asks for task ID + summary]

## Progress Saved
- Task: TASK-42
- Summary: Added login form and validation
- Commit: a1b2c3d
- Status: Still IN PROGRESS

Ready to continue on another machine.
```

- No tests required (speed is priority)
- WIP commit format: `wip(TASK-42): summary`
- Always pushes to remote

### `/master-plan:done` — Ship It

Runs tests, commits, pushes, and marks the task complete in MASTER_PLAN.md.

```
> /master-plan:done

[Shows changed files, asks for task ID + summary, runs tests]

## Task Complete
- Task: TASK-42
- Tests: ✅ Passed
- Commit: d4e5f6g — feat(TASK-42): Add user authentication
- Push: ✅ Pushed to origin
- MASTER_PLAN.md: Updated (3 locations)
```

- Auto-detects test command (`npm test`, `pytest`, `cargo test`, `go test`, `make test`)
- Updates ALL task locations in MASTER_PLAN.md (table + header + bullets)
- Conventional commit format: `feat(TASK-42): summary`

## MASTER_PLAN.md Format

The plugin expects tasks in this format:

```markdown
### TASK-42: Add user authentication (IN PROGRESS)
**Priority**: P1
**Status**: IN PROGRESS (2026-02-23)

Description of the task...

**Tasks**:
- [ ] First step
- [x] Completed step
```

Statuses: `PLANNED`, `IN PROGRESS`, `REVIEW`, `PAUSED`, `✅ DONE`

A full template is included at `templates/MASTER_PLAN.md`.

## Task ID Format

| Prefix | Usage |
|--------|-------|
| `TASK-XXX` | Features and improvements |
| `BUG-XXX` | Bug fixes |
| `FEATURE-XXX` | Major features |
| `ROAD-XXX` | Roadmap items |
| `IDEA-XXX` | Ideas for later |
| `ISSUE-XXX` | Known issues |
| `INQUIRY-XXX` | Investigations |

IDs are **globally sequential** across all types. If TASK-42 and BUG-43 exist, the next ID is 44.

## The Workflow

```
Start session
    │
    ▼
/master-plan:next          ← Pick a task (scored by priority)
    │
    ▼
  Work on it               ← Write code, debug, iterate
    │
    ├──► Need to stop?     → /master-plan:save (WIP commit + push)
    │                         Come back later, git pull, /next
    │
    ├──► Found a bug?      → /master-plan:task (log it, keep working)
    │
    └──► Done!             → /master-plan:done (test + commit + push + mark complete)
         │
         ▼
    /master-plan:next      ← Pick the next task
```

## Requirements

- [Claude Code](https://claude.com/claude-code) (or any Agent Skills-compatible tool)
- Git
- A project with (or ready for) a MASTER_PLAN.md file

No npm install, no build step, no external dependencies. The skills are pure markdown — Claude does the parsing.

## License

MIT
