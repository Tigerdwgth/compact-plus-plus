---
name: cpp
description: "Use when about to run /compact or context is getting long and you must not lose project/server-connection/git-branch/environment facts, OR when the user wants a handoff summary of the current task to copy to someone else / a future session. Snapshots the session's critical state to disk, prints a copy-ready handoff doc, and hands you a ready-to-run /compact command with a focus instruction. Usage: /cpp"
user_invocable: true
argument_description: "(no arguments)"
---

# cpp — Compact with Protection

`/compact` drops the middle of the history, and with it the one-off facts you confirmed this session: how to reach the servers, the current git branch and push target, the in-progress task, environments and paths, the gotchas you hit. `cpp` writes those facts to disk, prints a copy-ready handoff, and hands you a `/compact` command with the focus instruction inlined.

The handoff serves two readers: future you (needs exact detail to resume) and someone taking over (has none of your context, must see the current state and next step at a glance).

Use it when context is getting long before `/compact`, or when the user wants a handoff to pass on. Skip it when a session just started with no state worth keeping.

Output language: this file is English, but the runtime artifacts — the handoff and the focus instruction — follow the language the user is speaking. Chinese conversation, Chinese output.

## Steps

1. Scan the current conversation history (do not re-ask, do not guess) for six categories. Collect whatever appeared; mark anything absent as "not present in this session":
   - Server connections: hosts/aliases/IPs, users, jump hosts and ports, proxy, passwordless status, GPU layout — whatever was actually used or confirmed.
   - Git branch and repo state: repo path, branch, push-target remote (non-default ones like `cap-x→v0.0.1.1`), uncommitted changes, recent key commits. If the cwd is a git repo, verify by actually running, not from memory: `git -C <repo> rev-parse --abbrev-ref HEAD; git -C <repo> remote -v; git -C <repo> status -s | head -20`.
   - Project goal and in-progress task: what is being done, the goal, key decisions, blockers.
   - Environments and paths: conda/uv/pixi env names and paths, dataset/weights/checkpoint paths, working dir, key scripts.
   - Next-action checklist: ordered, executable actions the next person takes — each an action plus a checkable "done when …", not a restatement of the task status. No clear next step: write "not present in this session".
   - Known issues / gotchas: still-unresolved errors or counter-intuitive behavior, and traps worth flagging, each with symptom plus workaround-if-any — not just "it errored". Skip resolved ones.

2. Write the result as structured Markdown to `cpp_snapshot.md` in this session's scratchpad (path is in the "Scratchpad Directory" section of the system prompt). Keep all six headings even when empty. This copy is for future you: a precise technical listing, not full sentences. After compaction a single `Read` of it restores everything.

3. Rewrite that content into a handoff printed in the reply body — not a verbatim paste of the snapshot. This lands the info in recent context (which `/compact` keeps) and gives the user a block to copy onward. Open with 2–3 plain sentences (what project / where we are / what to do first), then a section per category under human headings. Keep every IP, path, and command exact, but in full sentences or clear bullets. Make the checklist ordered and checkable; give each gotcha its symptom and workaround. End by pointing at `cpp_snapshot.md` as the full-detail backup.

4. End the reply with a single, ready-to-run line, focus instruction inlined in the user's language, naming all six categories — vague ones like "don't lose important info" don't bias compaction. For example:
   ```
   /compact Keep: this session's server connections (host/IP/user/jump/port/proxy); the git repo path, branch, and push-target remote; the in-progress task and goal; env names and dataset/weights/checkpoint paths; the next-action checklist; known issues and gotchas. Snapshot saved at scratchpad/cpp_snapshot.md.
   ```
   Then tell the user: copy and run that line to compact; the info is in `cpp_snapshot.md` and you can `Read` it back after compaction; the handoff above can be sent to someone else. Do not call `/compact` yourself.

## Common Mistakes

- Writing the git branch or remote from memory instead of running the commands.
- Doing only one of disk-write and print. The disk copy guards against total loss; the printed copy lands in recent context so compaction keeps it. Both, always.
- Pasting the snapshot straight into the handoff. The snapshot is a technical reference for you; the handoff is rewritten prose someone without context can follow.
- Asking the user for facts instead of reading them from this session's history; mark whatever is absent as "not present in this session" rather than inventing it.
