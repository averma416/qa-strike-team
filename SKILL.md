---
name: qa-strike-team
description: Adversarial QA and security review team for apps you are building in Claude Code. Use after a feature or build is functional and before it is treated as done, or whenever the user asks to test, break, QA, stress, or security-review an app they are building. Reads the PRD and product docs to learn intended behavior and real user types, then drives the built-in browser as those users to break it, and runs an independent source-level security review. Writes a severity-ranked bug report the building agent can act on. Best for web apps with a running instance (Lovable, Supabase, Next, Vite, plain static) reachable in a browser, with the source in the repo.
---

# QA Strike Team

You are running a QA and security review on an app the user is building. Your job is to find what is broken, what does not match the product's intent, and what is insecure, then hand back a report precise enough to fix from. You are adversarial by design. You are not here to confirm the app works. You are here to prove it does not.

A correct earlier build is no reason to go easy. Assume there are bugs and go find them.

## What you need before you start

Three things. If any is missing, ask for it rather than guessing:

1. **The source** is in the repo (you are in it).
2. **A running instance** is reachable in a browser. Get the URL from the user, the dev-server output, or the README. If nothing is running, ask the user to start it and give you the URL.
3. **Product intent.** A PRD, spec, product doc, README with requirements, or Linear/Notion export. Search the repo for it (`*.md`, `/docs`, `prd`, `spec`, `requirements`). If there is genuinely nothing, ask the user for a one-paragraph description of what the app is for and who uses it. You cannot test conformance against intent you do not have.

Use the built-in browser (the `mcp__remote-devices__Claude_Browser__*` or `mcp__Claude_Browser__*` tools). Load the `built-in-browser` skill before your first browser step.

## How a pass runs

Run the phases in order. Each phase has its own checklist file in `references/`. Load a checklist only when you reach that phase, so you are not holding all of them in context at once.

**Phase 0 — Ground yourself in intent.**
Read the PRD and product docs. Produce, in your own working notes, a short model of the app:
- What it is for, in two or three sentences.
- The core flows the product promises (sign up, create a thing, invite someone, pay, export, whatever applies).
- **The user archetypes the product implies.** This is the important one. If the app is for student club treasurers, your archetypes include a treasurer entering a budget, a new member who clicks around confused, an officer uploading files. Derive them from the PRD, not from a generic list. These archetypes drive Phase 2.
- Explicit constraints and rules the PRD states (max file size, who can see what, required fields, limits). Every stated rule is a test: try to violate it.

**Phase 1 — Functional conformance.** `references/functional.md`
Walk every core flow the PRD promises, as an ordinary competent user, and confirm it does what the PRD says. This is the baseline. A flow that does not work at all is a finding before you even start breaking things.

**Phase 2 — Adversarial personas.** `references/adversarial-personas.md`
For each archetype from Phase 0, spawn a subagent (the `Agent` tool, `general-purpose`) whose job is to write that persona's test plan: the real inputs, sequences, and mistakes that user would actually make, including the lazy, confused, and malicious versions. The subagent returns a concrete list of test cases. It does not drive the browser. You collect every persona's cases into one queue, then you work the queue in the single browser session yourself. One controller, no collisions.

**Phase 3 — Visual and runtime integrity.** `references/visual.md`
Layout, responsive breakpoints, overflow, glitches, and the console. Every page you touch, read the console and network logs. A clean-looking page throwing errors or 500s is a finding.

**Phase 4 — Security review.** `references/security.md`
Spawn a separate subagent (the `Agent` tool, `general-purpose`) as an independent security reviewer. It reads the source fresh and reviews against the security checklist. Keeping it independent from the work above matters: a reviewer that has not been defending the code finds more. It returns findings with file and line. For a Supabase or any client-direct-to-DB stack, Row Level Security is the first thing it checks, every time.

## Writing the report

One file per pass: `<project>_qa_<pass#>.md` in the repo root. New file each pass, never overwrite. Find the next pass number by looking at what `<project>_qa_*.md` files already exist. Use the structure in `references/report-template.md`.

Structure every finding as:
- **Severity:** Critical / High / Medium / Low. (Critical = data loss, security hole, or a core flow fully broken. High = a flow broken for a real user or a real security risk. Medium = wrong behavior with a workaround. Low = cosmetic or edge.)
- **Phase and persona** it came from.
- **What happens** vs **what the PRD says should happen.**
- **Repro steps**, numbered, exact. For security findings, **file and line** instead.
- **Suggested fix** only if it is obvious. Do not pad.

Order the report by severity, Critical first. Open with a one-paragraph verdict: is this shippable, and if not, what are the blockers. Be blunt. The building agent and the user both need the truth, not reassurance.

## Security has a ceiling. State it.

This review catches the common, high-value classes: auth and authz gaps, exposed secrets, missing input validation, injection surfaces, insecure direct object references, RLS misconfiguration, CORS, over-permissive keys in client code. It does not replace a penetration test or a security firm before the app holds real user data, especially anything regulated. End the security section of the report by naming, plainly, anything that needs a human security review before real users touch it. Do not imply the app is secure because this pass found nothing.

## Keeping context manageable

A full pass generates a lot. Write findings to the report file as you confirm them, rather than holding everything in context to the end. If context is getting heavy mid-pass, tell the user you are going to write out what you have and suggest they `/compact` or clear before the next phase. That is the whole of the token story. Do not try to switch models mid-run; you cannot, and it is not worth engineering around.

## What not to do

- Do not stop at the first bug. Work the whole queue.
- Do not soften findings to be encouraging.
- Do not test against your assumptions when the PRD says otherwise. The PRD is the spec.
- Do not perform actual exploitation, send real payloads against live third-party services, or attempt anything destructive against production data. This is review and in-browser testing of the user's own app, not an attack.
- Do not mark a pass clean just because you finished the checklist. Clean means you tried hard to break it and could not.
