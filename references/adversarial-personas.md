# Phase 2 — Adversarial personas

This is the heart of the skill. Generic "try bad input" testing misses what real users actually do. Real breakage comes from real usage patterns, so you test as the people the product is actually for.

## How it works

For each user archetype you derived in Phase 0, spawn a subagent (the `Agent` tool, `general-purpose`). Each subagent's job is to **write that persona's test plan**, not to drive the browser. One controller drives the browser later. This keeps each persona's reasoning in its own context and avoids collisions.

Give each subagent:
- The app's purpose and the relevant PRD excerpts.
- The one persona it is embodying, described concretely (who they are, what they are trying to get done, their skill level, their incentives).
- The flows that persona touches.

Ask each subagent to return a concrete, numbered list of test cases: the exact inputs, sequences, and mistakes that persona would actually make. Not categories, actual values. "Enter 1,500.00 with the comma in the budget field," not "test number formatting."

## What each persona should cover

Tell each subagent to think across these modes for its persona:

- **The honest case done sloppily.** The real input a tired, distracted, or rushed version of this user types. Trailing spaces, wrong date format, pasted text with formatting, double-clicks, hitting back mid-submit, refreshing during an upload.
- **The confused case.** This user misreads the UI. Fills fields in the wrong order, puts an email in the name box, uploads the wrong file type, abandons halfway and comes back.
- **The boundary case for this persona's data.** The treasurer who enters a budget of 0, or negative, or 99999999999. The organizer who creates an event in the past. The longest realistic name, the emoji in the text field, the apostrophe in O'Brien.
- **The greedy case.** This user tries to do more than intended. Uploads a huge file. Creates a hundred items. Submits the same form ten times fast. Opens the same thing in two tabs and edits both.
- **The malicious case, within this persona's reach.** Not a pentest (Phase 4 owns security). This is the ordinary user who pokes: edits a URL to see someone else's record, changes a form value in dev tools, tries to access a page they should not. Note these as both functional and potential security findings.

## Running the queue

Collect every persona's returned test cases into one queue. Then **you** work the queue in the single browser session. For each case:

1. Execute it exactly as written.
2. Observe what happens: the UI response, the console, the network tab.
3. If it breaks, misbehaves, or diverges from the PRD, it is a finding. Record severity, the persona, exact repro steps, expected vs actual.
4. Move to the next. Do not stop at the first bug.

A case that the app handles correctly is not worth logging. Log what breaks and what behaves wrong.
