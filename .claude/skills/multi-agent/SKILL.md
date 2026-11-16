---
name: multi-agent
description: >-
  Coordinate multiple agents working in parallel worktrees on this project:
  splitting work, dispatching workers, and collecting results. Use when running
  more than one agent at a time, spawning worktrees for parallel tasks, or
  working as a dispatched worker in one of them.
---

# Multi-Agent

Basic rules for running several agents in parallel on this project. Each agent gets its
own worktree and owns it alone.

## Worktree independence

Changes made in one worktree can never override another worktree's prompt. This is what
keeps the agents independent.

- A worker writes only inside its own worktree. Never write, edit, move, or delete files
  under another worktree's path.
- Never modify another worker's prompt, instructions, task definition, `CLAUDE.md`, skill
  files, or agent definitions — not to "help", not to unblock yourself, not even when the
  other worker looks idle or wrong.
- Shared and global configuration (`~/.claude`, this repo's `.claude/` on another branch,
  instruction files other workers read) is read-only to a dispatched worker. If it
  genuinely needs to change, escalate to the coordinator instead of editing it.
- Coordination happens through messages to the coordinator — never by editing another
  worker's inputs.
- A coordinator may dispatch and re-dispatch tasks, but it writes prompts only through
  task dispatch, not by rewriting files inside a worker's worktree.

## Splitting work

- Give each worker a scope that does not overlap another worker's files. Overlap is the
  usual cause of agents clobbering each other.
- State the deliverable per worker: what to change, what "done" looks like, what to
  report back.
- If two tasks must touch the same file, run them in sequence, not in parallel.

## Reporting back

- A worker reports results to the coordinator and stops. It does not merge into another
  worker's branch.
- The coordinator integrates the results and resolves conflicts.
- If a worker is blocked, it says so and waits — it does not reach outside its worktree
  to unblock itself.
