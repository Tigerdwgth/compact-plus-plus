# compact-plus-plus (cpp)

A Claude Code skill: **compact with protection**. Before `/compact` throws away the middle of your history, `cpp` saves the state worth keeping and prints a handoff you can hand to anyone.

## English

Claude Code's `/compact` compresses the middle of the conversation, and it will happily drop the hard-won facts you confirmed along the way: how to reach your servers, which branch you're on and where it pushes, the task you're halfway through, your environments and paths, and the gotchas you just hit.

Before compacting, `cpp` writes those facts to a file, prints a copy-ready handoff doc, and hands you a `/compact` command with the focus instruction already inlined. Even if compaction drops the history, a single `Read` of the snapshot brings it all back.

The handoff doc is written for two readers: future you (who needs precise detail to resume), and someone else taking over (who has none of your context and must grasp, at a glance, what state things are in and what to do next).

### Install

```bash
git clone https://github.com/Tigerdwgth/Compact-Plus-Plus.git ~/.claude/skills/cpp
```

Then just type `/cpp` in Claude Code — no arguments needed.

### When to use it

- Context is getting long and you're about to `/compact`.
- The session has established one-off facts you can't look back up from code or CLAUDE.md and that compaction will erase (a temporary port, this session's push target, an experiment path, a gotcha you just hit).
- You want a handoff you can pass to a teammate or to a future session.

> Note: the skill can't touch the `/compact` button — only you can press it. `cpp` just gets the info saved and the command ready.

## 中文

Claude Code 的 `/compact` 会压掉中间历史,顺手把你这一路辛苦确认的东西也丢了:服务器怎么连、当前在哪个分支推去哪、跑到一半的任务、环境和路径、刚踩过的坑。

`cpp` 在压缩前先把这些存成文件、再打印一份能直接复制的交接文档,最后给你一条带好 focus 指令的 `/compact` 命令。压缩就算丢了历史,`Read` 一下快照文件全都回来。

交接文档给两种人看:压缩后的你自己(要精确细节续上),和接手的另一个人(没你的上下文,得一眼看懂现在啥状态、下一步干啥)。

### 装

```bash
git clone https://github.com/Tigerdwgth/Compact-Plus-Plus.git ~/.claude/skills/cpp
```

然后在 Claude Code 里敲 `/cpp` 就行,不用带参数。

### 什么时候用

- 上下文长了、准备 `/compact` 之前
- 会话里定过一些代码和 CLAUDE.md 里查不到、压缩后就没了的一次性信息(临时端口、这次推去哪、实验路径、刚踩的坑)
- 想要一份能甩给别人或未来自己的交接说明

> 注:技能碰不到 `/compact` 那个按钮,真正的压缩得你自己按。cpp 只负责把信息存好、命令备好。

## License

[MIT](./LICENSE)
