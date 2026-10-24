# QA Strike Team

An adversarial QA and security review skill for apps you build with agentic coding tools. You point it at a running app and its repo, and it tries to break the thing the way real users would, then hands back a bug report precise enough to fix from.

It is built for the build-then-test loop: you ship a feature, you run this before calling it done.

## What it does

A full pass runs in five phases, each with its own checklist:

- **Phase 0 — Intent.** Reads your PRD or product docs to learn what the app is supposed to do and who actually uses it.
- **Phase 1 — Functional.** Walks every core flow and confirms it matches the spec.
- **Phase 2 — Adversarial personas.** Derives the real user types from the PRD, spawns a subagent per persona to write that user's test plan with real inputs and real mistakes, then runs every case in the browser. This is the part that finds what generic testing misses.
- **Phase 3 — Visual and runtime.** Layout at phone, tablet, and desktop widths, overflow, broken states, plus the console and network tab on every page.
- **Phase 4 — Security.** An independent reviewer reads the source fresh and checks auth, authorization, secrets, input handling, and config. For Supabase and similar stacks it checks Row Level Security first, every time.

Output is a single file per pass, `<project>_qa_<pass#>.md`, ordered by severity with repro steps.

## Scope and limits

This is review plus in-browser testing of your own app. It is not a penetration test and it does not run live attacks against third-party services. Every security report ends with a plain statement of what still needs a human security review before real users and real data, especially anything handling payments or regulated data. Do not read a clean pass as proof the app is secure.

## Requirements

- Claude Code with the built-in browser available.
- The app's source in the repo you run it from.
- A running instance reachable in a browser.
- A PRD, spec, or product doc. If there is none, it will ask you to describe the app in a paragraph.

## Install

Clone or copy this into your Claude Code skills directory:

```
~/.claude/skills/qa-strike-team
```

Or scope it to one project at `.claude/skills/qa-strike-team` inside that repo.

## Use

Open Claude Code in your app's repo with the dev server running, then ask it to run the QA strike team, or invoke the skill by name. It will ask for the URL and the PRD if it cannot find them, then work through the phases and write the report to the repo root.

## Structure

```
qa-strike-team/
  SKILL.md                        orchestrator
  references/
    functional.md                 Phase 1 checklist
    adversarial-personas.md        Phase 2 engine
    visual.md                      Phase 3 checklist
    security.md                    Phase 4 checklist
    report-template.md             output format
```

## License

MIT. Use it, fork it, change it.
