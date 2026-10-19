# actually-right

what my claude runs on. steal it.

A `CLAUDE.md` and three skills I use every day with Claude Code. Less slop, fewer redos, and a Claude that proves its work before calling it done.

## What's inside

**`CLAUDE.md`** – global rules for every session:
- Root-cause fixes only, no temporary patches
- Simple code, and comments only when the *why* isn't obvious
- Writing that sounds human: US English, no em dashes, a real spelling and grammar pass
- Nothing is done until it's proven to work
- Asks before spawning subagents
- A `tasks/` system (task map, todo, lessons) for multi-step work
- Code analysis across six categories, in loop or manual mode, with no false positives
- A few platform conventions (103 Early Hints, Cloudflare Pages static assets)

**`skills/design-web`** – web frontend design. Typography, color, layout, motion, states, accessibility and performance. Can diagnose a UI without being told what's wrong.

**`skills/design-native`** – native and Rust GUI design. iOS, Android, desktop, Slint and friends, plus watch, TV, automotive and embedded.

**`skills/rust-perf`** – Rust performance. Runtime speed, memory, tail latency, binary size and compile times, with honest measuring first.

## Install

```bash
git clone https://github.com/zztaeha/actually-right.git
```

```bash
cd actually-right
```

```bash
cp CLAUDE.md ~/.claude/CLAUDE.md
```

```bash
mkdir -p ~/.claude/skills && cp -R skills/* ~/.claude/skills/
```

Heads up: the `cp` above replaces any `CLAUDE.md` you already have. Back yours up first, or merge the parts you like.

## Use

The skills activate when you ask for them:

```
use skill: design-web
use skill: design-native
use skill: rust-perf
```

## Make it yours

It's opinionated on purpose. Change the language, drop the rules you don't need, and add your own. The best `CLAUDE.md` is the one that fixes the mistakes *your* Claude keeps making.

## License

[MIT](LICENSE)
