# master-plan

![master-plan cover](cover.png)

AI-native task management for [Claude Code](https://claude.com/claude-code). Track tasks in a `MASTER_PLAN.md` file, pick what to work on, save progress, and ship — all through agent skills.

```
/master-plan:task  →  create  →  /master-plan:next  →  pick  →  /master-plan:save  →  pause  →  /master-plan:done  →  ship
```

## Quick Start

**Step 1** — Install the plugin (run these two lines inside Claude Code):

```
/plugin marketplace add endlessblink/master-plan
/plugin install master-plan
```

**Step 2** — Create your first task:

```
/master-plan:task
```

That's it. If your project doesn't have a `MASTER_PLAN.md` yet, the plugin creates one automatically in `docs/MASTER_PLAN.md`.

**Step 3** — Work the loop:

```
/master-plan:next     Pick what to work on
                      ... write code ...
/master-plan:save     End of session? Save WIP + push
                      ... next day, different machine ...
/master-plan:next     Pick up where you left off
                      ... finish the task ...
/master-plan:done     Test + commit + push + mark complete
```

> **Already have tasks in a MASTER_PLAN.md?** The plugin works with any existing file as long as tasks use `### TASK-123: Title (STATUS)` headers. Run `/master-plan:next` to start picking from your existing backlog.

## Why?

- **Zero infrastructure** — Just a markdown file + git. No Jira, no Linear, no database.
- **AI-native** — Claude reads the plan, scores priorities, picks tasks, updates status, runs tests, commits. You just approve.
- **Session-resilient** — `/save` your work, switch machines, come back tomorrow, `/next` picks up where you left off.
- **Self-documenting** — The MASTER_PLAN.md IS the project history. Nothing to keep in sync.
- **Any language, any framework** — Works with Node, Python, Rust, Go, or anything else. Auto-detects your test runner.
- **Cross-platform** — Built on the [Agent Skills open standard](https://agentskills.io). Core functionality works in Claude Code, Cursor, GitHub Copilot, Windsurf, and other compatible tools. Interactive task picking is best in Claude Code.

## Skills

### `/master-plan:task` — Create a Task

Creates a new task with an auto-generated sequential ID. If no `MASTER_PLAN.md` exists, creates one from a starter template.

```
> /master-plan:task

Using task number: 42

? Type: TASK / BUG / FEATURE / INQUIRY
? Priority: P0 (critical) → P3 (backlog)
? Title: Add user authentication

Task added to MASTER_PLAN.md:
- ID: TASK-42
- Title: Add user authentication
- Priority: P1
- Status: PLANNED
```

### `/master-plan:next` — Pick What to Work On

Reads your MASTER_PLAN.md, scores tasks by priority and status, shows an interactive picker. Highlights in-progress tasks so you finish what you started.

```
> /master-plan:next

You have 1 task in progress.

? Which task would you like to work on?
  ● TASK-42: Add user authentication    P1 · PLANNED
    BUG-38: Fix login redirect          P0 · PLANNED
    TASK-40: Refactor API layer         P2 · IN PROGRESS
    Show more tasks...

> [Selects TASK-42, shows full details, offers to start working]
```

**Filters:** `/master-plan:next bugs` · `/master-plan:next planned` · `/master-plan:next active` · `/master-plan:next review`

### `/master-plan:save` — Save Work-in-Progress

Commits and pushes without marking the task done. No tests, no ceremony — just save and go. Perfect for switching machines or ending a session.

```
> /master-plan:save

## Progress Saved
- Task: TASK-42
- Summary: Added login form and validation
- Commit: a1b2c3d — wip(TASK-42): Added login form and validation
- Status: Still IN PROGRESS

Ready to continue on another machine:
  git pull → /master-plan:next
```

### `/master-plan:done` — Ship It

The full completion workflow: run tests, commit, push, and mark the task done in all locations within MASTER_PLAN.md.

```
> /master-plan:done

## Task Complete
- Task: TASK-42
- Tests: ✅ Passed
- Commit: d4e5f6g — feat(TASK-42): Add user authentication
- Push: ✅ Pushed to origin
- MASTER_PLAN.md: ✅ Updated (table + header + bullets)
```

**Test auto-detection:** `npm test` · `pytest` · `cargo test` · `go test ./...` · `make test`

**Commit format:** `feat(TASK-XXX):` for features, `fix(BUG-XXX):` for bugs, `wip(TASK-XXX):` for saves.

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

## MASTER_PLAN.md Format

The plugin reads and writes tasks in this format:

```markdown
### TASK-42: Add user authentication (IN PROGRESS)
**Priority**: P1
**Status**: IN PROGRESS (2026-02-23)

Description of the task...

**Tasks**:
- [ ] Create login form
- [x] Set up auth provider
```

**Statuses:** `PLANNED` · `IN PROGRESS` · `REVIEW` · `PAUSED` · `✅ DONE`

**When a task is marked done**, it gets strikethrough on the ID and a status update:

```markdown
### ~~TASK-42~~: Add user authentication (✅ DONE)
```

Tasks can also appear in a roadmap table — the plugin updates both locations automatically.

> **Starting fresh?** Run `/master-plan:task` and the plugin creates the file for you. A starter template is also available at [`templates/MASTER_PLAN.md`](templates/MASTER_PLAN.md).

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

## Team Install

Commit this to your project's `.claude/settings.json` and teammates are prompted to install automatically:

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

## Requirements

- [Claude Code](https://claude.com/claude-code) (or any [Agent Skills](https://agentskills.io)-compatible tool)
- Git

No npm install, no build step, no external dependencies. The skills are pure markdown — Claude does the parsing.

## Contributing

Issues and PRs welcome at [github.com/endlessblink/master-plan](https://github.com/endlessblink/master-plan/issues).

## License

MIT
