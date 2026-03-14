---
name: next
description: Analyze and pick the next task to work on. Reads MASTER_PLAN.md, scores tasks by priority and status, and presents interactive selection. Use when starting a session or deciding what to tackle next.
---

# What's Next?

Analyze MASTER_PLAN.md tasks, score by priority/status, and let the user pick interactively.


## Rules

1. **Finish before starting** — Always highlight IN PROGRESS tasks first
2. **P0 trumps all** — Critical issues come first regardless of status
3. **Interactive selection** — Always use AskUserQuestion, never just print a list
4. **Context on selection** — Always show full task details when picked
5. **Action oriented** — Offer to start work immediately
