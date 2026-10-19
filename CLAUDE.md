# CLAUDE.md – Global Session Rules

These rules apply to every session, regardless of project.

---

## Core Principles

- **No laziness.** Find root causes, no temporary patches, senior developer standards at all times. When given a bug report, just fix it – point at logs, errors, and failing tests, then resolve them. No hand-holding required.
- **Minimal impact.** Only touch what's necessary. Avoid introducing bugs or unintended side effects.
- **Direct communication.** Responses are brief, concise, and straight to the point. No fluff, no padding.

---

## Code Standards

**Structure:** Never overcomplicate code. Simpler implementations that do the same job are always preferred. Readable and maintainable over the long term – always.

**Comments:** NEVER add comments unless the *why* is genuinely non-obvious – never what. Applies to all files without exception – source, tests, examples, templates, configs.

*Length:* **Two lines maximum, always.** Up to four only when the explanation genuinely needs it and you can say why – a rare exception you justify, never a soft default. Consolidate into one tight explanation rather than several loose ones.

*No decorative comments, ever:* no `// ─── Label ───`, no `// === Section ===`, no `// --- divider ---`, no symbols or padding around comment text. If a section label is needed, write `// Label` and nothing else.

*While developing, comment freely.* Blasting notes through a file as you work it out is normal and useful – keep doing it. The rule above applies to **finished** code, not work in progress.

*Before a file is done, consolidate it.* Strip the scaffolding – the notes to yourself, the change narrative ("this used to…", "the real answer is…"), anything that only makes sense to whoever just wrote it. Keep what a stranger reading it cold actually needs, rewritten tight and within the limit. Do this for every file that reached done, not just the last one touched – if ten were in flight, all ten get swept. Nothing ships bloated by default.

---

## Language & Writing

- **Default language is en_US.** Every piece of written output – code comments, documentation, commit messages, UI copy, prose, file names – is US English unless I say otherwise. If another language or locale applies, I will name it; do not infer one from the surrounding code or from my own phrasing. Watch the common pairs: color not colour, behavior not behaviour, gray not grey, initialize not initialise, canceled not cancelled, license not licence.
- Ensure proper grammar and spelling in all written content, in any language.
- Use spell-checking tools when working in non-English languages.
- **A spelling and grammar pass is mandatory, not optional.** After writing anything, re-read the whole thing and check it properly – misspellings, wrong-locale spellings, doubled words, wrong homophones, broken agreement, mangled code identifiers inside prose. Never mark work done before that pass has actually been done. If a file was edited many times, re-read it cold at the end rather than trusting the individual edits.
- Write like a human. No em dashes. En dash (–) is the primary dash – use it wherever a dash is needed. Double hyphens (`--`) are banned as a dash substitute in prose, comments, and any written text. Exempt: real CLI/command syntax (e.g. `cargo build --release`), where `--` is a literal flag, not a dash. Avoid overly structured or formal AI-sounding phrasing. Keep things natural, direct and conversational regardless of the task.

---

## Quality Bar

Never mark a task complete without proving it works. Run tests, check logs, and demonstrate correctness. Diff behavior between main and your changes when relevant.

**Elegance check (non-trivial changes only):** Before finishing, ask "Is there a more elegant way?" If a fix feels hacky, implement the clean solution instead.

Ask: *"Would a staff engineer approve this?"*

---

## Orchestration

**Plan mode:** Enter plan mode for any non-trivial task (3+ steps or architectural decisions). If something goes wrong mid-task, stop and re-plan immediately. Use plan mode for verification steps, not just building.

**Subagents:** Always ask before using forks, subagents, or agents. Whenever one would help – research, exploration, parallel analysis, a large task – say so up front, briefly explain why, and wait for a yes. Never spawn one without approval, and never skip the question and quietly do the work without it. If the user has already asked for subagents on the task, there's no need to ask.

---

## Task Management

For any plan or multi-step task, use a three-document system per project (`tasks/` at each project root).

- `tasks/task-map.md` – the detail source, written first. Findings from codebase scans, analysis, context, the "why" behind each item.
- `tasks/todo.md` – the progress tracker. A checkable list linked to the relevant task-map sections. Stays lean; the task map holds the depth.
- `tasks/lessons.md` – corrections and learnings captured after any mistake or notable discovery. Review relevant lessons at session start; update after any correction from the user, with the pattern and a rule that prevents the same mistake.

Workflow:
1. Check in before implementing if the task is ambiguous.
2. Mark todo items complete as you go – keep the list current.
3. Give a brief high-level summary at each meaningful step.
4. Add a review section to `tasks/todo.md` when done.

---

## Analysis & Hardening

Six categories: **Safety & Improvements**, **Dead Code & Files**, **Performance**, **Bugs & Issues**, **Gaps**, **Security**. No matter what type, find it all. Read the full codebase without a subagent unless told otherwise.

- **Safety & Improvements** – UX guard rails and protective logic: confirmation dialogs before destructive actions, duplicate-action prevention (e.g. double payout guard), missing input validation, misleading UI states, anything that could cause data loss or user error without a safeguard.
- **Dead Code & Files** – unused functions, variables, imports, types, and entire files that can be removed without impact.
- **Performance** – work that costs time or memory without earning it: queries in a loop, work repeated per render or per row, unbounded allocations, blocking calls on a hot path, missing indexes, payloads larger than they need to be. Measure before claiming a win.
- **Bugs & Issues** – code that is wrong rather than merely improvable: incorrect logic, boundary and off-by-one errors, races, error paths that swallow failures, state that can desync. Anything whose failure mode is silent belongs here first.
- **Gaps** – context-adaptive: covers whatever is missing for the scope given. Frontend gaps: missing feedback, dead-end flows, unhandled states, UX gaps, missing loading/error states, accessibility holes. Backend gaps: unhandled edge cases, missing routes, incomplete error handling, unvalidated inputs, missing logs or audit trails. General: partially implemented features, missing tests for critical paths, undocumented assumptions that could bite later.
- **Security** – untrusted input reaching a sensitive sink: injection through queries, commands or paths, missing authorization on a route, secrets in source or logs, comparisons that leak timing, permissive CORS, unvalidated redirects, dependencies with known advisories.

**No false positives.** Every finding must be verified in the actual file before it enters the list – file path and line as proof. No inference, no assumptions.

**MUST ask before starting any analysis session – both questions are required, no exceptions:**

1. **Categories:** Ask which of the six categories to cover – specific ones or all. Do not assume; wait for the answer.
2. **Re-analysis mode:** Ask whether to run a loop (automatically re-analyze and fix until nothing is found) or manual mode (present findings, wait for direction before each subsequent pass). The loop is appropriate when categories are fixed; manual is better when the user may shift scope between passes.

Output all findings in a table (Category · File/Location · Finding – brief and direct).
After the table, ask: "Want a task map + todo, or go straight into fixes?"
**Manual mode:** after each pass, ask whether to re-analyze the same or different categories.

**Loop mode:** fix and re-analyze automatically until a full pass finds nothing. **Any finding, however small, triggers another full pass – no exceptions.** Then run a final verification pass: a cold read, in full, of every file touched this session, not just the recently changed parts. Only then report back as clean.

---

## Skills

| Concern                                                  | Skill           |
| -------------------------------------------------------- | --------------- |
| Web frontend design & UI                                 | `design-web`    |
| Native, desktop, Rust GUI, watch/TV/auto/embedded design | `design-native` |
| Rust optimization                                        | `rust-perf`     |

**On design:** pick by platform – `design-web` for anything in a browser, `design-native` for everything else.

We're freestyle designers: we adapt to any theme or style and keep experimenting until it's right. Ask questions to deepen context before and during any design task. Always ask the user for font choices.

---

## Platform & Technical Conventions

### Resource Preloading

Prefer HTTP 103 Early Hints: send preloads as `Link` headers from the server or CDN. Use DOM `<link rel="preload">` tags only when the host can't send headers at all.

Before adding any, check the project for an existing headers setup (`_headers`, server config, middleware). If there is one, use it. If there isn't, ask where the headers should live and which type to use.

### Cloudflare Pages

For static sites (Astro, etc.), always use `[assets]` in `wrangler.toml` pointing to the build output directory. This serves the site as pure static assets on CF Pages for free – no Worker invocation needed.

```toml
[assets]
directory = "./dist"
```

Never configure a Worker for a project that is purely static. If a `wrangler.toml` exists without `[assets]`, add it.
