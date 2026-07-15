---
name: cpp
description: "Use when about to run /compact or context is getting long and you must not lose project/server-connection/git-branch/environment facts, OR when the user wants a handoff summary of the current task to copy to someone else / a future session. Snapshots the session's critical state to disk, prints a copy-ready handoff doc, and hands you a ready-to-run /compact command with a focus instruction. Usage: /cpp"
user_invocable: true
argument_description: "(no arguments)"
---

# cpp — Compact with Protection

## Overview

When `/compact` compresses the context it drops the middle of the history, and with it the hard-won facts you confirmed in this session: **how to reach the servers, the current git branch / push target, the in-progress task, environments and paths, and the gotchas you already hit.** Before that compression, `cpp` **writes those critical facts to disk and prints a copy-ready handoff doc**, then hands you a `/compact` command with an inlined focus instruction.

The handoff doc has two readers, both essential:
- **Future you** (the same session after compaction, or a brand-new session) — needs precise technical detail to resume seamlessly.
- **Someone taking over** (a teammate, another agent) — has none of this session's hidden context and must understand "what state are we in, and what do I do next".

**Output language:** this file is written in English, but the artifacts the skill produces at runtime — the handoff doc in Step 3 and the focus instruction in Step 4 — must be written **in the same language the user is speaking in this conversation**. If the user is speaking Chinese, produce them in Chinese. The headings and mechanics below are for you; the reader-facing output follows the user.

## When to Use

- Context is getting long and you are about to `/compact`.
- The session has established one-off facts that are not in the code or CLAUDE.md and cannot be looked back up after compaction (a server's temporary port, this session's branch push target, a half-finished experiment path, a gotcha you just hit).
- The user wants a "handoff" they can copy to someone else or to a future session.
- Do NOT use it when a session just started and there is no state worth preserving.

## Steps to Execute

### Step 1: Scan this session for critical information (six categories)

From the **current conversation history** (do not re-ask the user, do not guess), extract the following six categories. Collect whatever appeared in this session; mark any category that did not come up as "not present in this session":

1. **Server connection info** — hostnames / aliases / IPs, usernames, jump hosts and ports, proxy settings, passwordless-login status, GPU layout, and any other connection details actually used or confirmed this session.
2. **Current git branch and repo state** — repo path, current branch name, push target remote (non-default targets like `GSJ_Thesis→abab` or `cap-x→v0.0.1.1`), a summary of uncommitted changes, recent key commits.
   - If the working directory is a git repo, **actually run** the commands below to verify — do not rely on memory:
     ```bash
     git -C <repo> rev-parse --abbrev-ref HEAD 2>/dev/null; git -C <repo> remote -v 2>/dev/null; git -C <repo> status -s 2>/dev/null | head -20
     ```
3. **Project goal and in-progress task** — what is being done right now, the goal, key decisions, blockers, so work can resume seamlessly after compaction.
4. **Environments and paths** — conda / uv / pixi environment names and paths, dataset / weights / checkpoint paths, working directory, key script locations.
5. **Next-action checklist** — an explicit, executable TODO list, in order, of the concrete actions the person taking over should perform (not a restatement of category 3's status). Each item should be one action plus a checkable "done when …" criterion. If there is no clear next step, write "not present in this session" — do not invent one.
6. **Known issues / gotchas** — errors, exceptions, or counter-intuitive behavior that came up this session but are **still unresolved**, plus traps worth flagging so the next person does not repeat them (e.g. "this command looks right but actually causes X"). Skip gotchas that are already resolved and no longer matter; spell out any workaround currently in effect.

### Step 2: Write to the scratchpad

Write the Step 1 results as structured Markdown to `cpp_snapshot.md` in this session's scratchpad directory (see the "Scratchpad Directory" section of the system prompt for the path). Use the same six second-level headings; keep the heading even for missing categories and write "not present in this session". This version is for **future you** and may be a precise technical listing (paths, commands, parameters) rather than full sentences. That way, even if compaction drops the history, a single `Read` of this file restores all critical state and technical detail.

### Step 3: Print a copy-ready handoff doc

**Rewrite** the Step 1 content into a handoff doc printed in the reply body — do not paste the Step 2 technical listing verbatim. This serves two purposes: it puts the information into the most recent context (which `/compact` almost always keeps), and it gives the user a block they can copy unchanged to someone else or into a new session. **Write it in the user's language** (see "Output language" above).

Rewrite requirements:
- Open with a 2–3 sentence plain-language overview: "what project / where we are now / what the person taking over should do first", so someone with zero background grasps the whole picture instantly, then expand into the six sub-sections.
- Give each category its own section with a human heading (e.g. "How to reach the servers" rather than a dry "Server connection info"), keeping technical detail exact (IPs, paths, commands must not be dropped or mis-transcribed) but presented as full sentences or clear bullets, not a keyword dump.
- Present the "next-action checklist" as an ordered, checkable, executable list.
- For "known issues / gotchas", spell out **the symptom + the known workaround (if any)**, not just "it errored".
- End with a line pointing at the on-disk file, e.g. "Full technical detail is saved in `cpp_snapshot.md`; after compaction, have Claude read that file to restore it."

### Step 4: Hand over a one-shot /compact command

At the end of the reply, give a **single line, ready to copy and run** compaction command, with the focus instruction inlined (in the user's language) covering all six categories, e.g.:

```
/compact Keep: this session's server connections (host/IP/user/jump/port/proxy); the current git repo path, branch, and push-target remote; the in-progress task and goal; conda/uv/pixi env names and dataset/weights/checkpoint paths; the next-action checklist; known issues and gotchas. Snapshot is saved at scratchpad/cpp_snapshot.md.
```

Then tell the user explicitly: **"Copy and run the line above to compact; the info is backed up to `cpp_snapshot.md` and I can `Read` it back anytime after compaction. The handoff doc above can also be copied and sent to someone else."** Do not call `/compact` yourself.

## Quick Reference

| Step | Action | Output | Reader |
|------|--------|--------|--------|
| 1 | Scan history for the six categories (verify git state by actually running commands) | internal list | — |
| 2 | Write to scratchpad/`cpp_snapshot.md` (technical listing, precision first) | on-disk file | future you |
| 3 | Rewrite into a handoff doc printed in the reply (narrative, readability first) | into recent context | future you + the person taking over |
| 4 | Give a one-shot `/compact <focus>` and prompt the user to run it | user compacts manually | the user |

## Common Mistakes

- ❌ **Writing the git branch / remote from memory**: you must actually run `git` commands to verify current state.
- ❌ **Printing without writing to disk (or writing without printing)**: do both — the disk copy guards against total loss, the printed copy lands in recent context so compaction does not cut it.
- ❌ **Copying Step 2 straight into Step 3**: the on-disk version is a technical reference for yourself; the printed version must be rewritten into full sentences with human headings so someone without context can follow it. Different purposes — not one content pasted twice.
- ❌ **Asking the user "which server is yours"**: the info comes from this session's history, not a fresh survey; whatever did not appear, mark honestly as "not present in this session".
- ❌ **Writing the "next-action checklist" as a status recap**: write executable actions ("do X until Y holds"), not a re-copy of category 3's project background.
- ❌ **"Known issues" that just say "it errored"** without symptom and workaround: the next person needs to know exactly what failed and what has already been tried, or it is worthless.
- ❌ **Inventing next steps or gotchas**: when the session has no clear next step or unresolved issue, write "not present in this session" honestly rather than padding for completeness.
- ❌ **Too-vague focus instruction** (e.g. `/compact don't lose important info`): it must name the six concrete categories so compaction is actually biased toward keeping them.
