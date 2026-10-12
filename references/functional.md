# Phase 1 — Functional conformance

Goal: confirm the app does what the PRD promises, as a competent ordinary user. This is the baseline before you try to break anything. If a core flow is broken here, that is a finding already.

Work through every core flow identified in Phase 0. For each flow:

- Complete it start to finish the intended way. Does it succeed?
- Does the end state match what the PRD says should happen? (Record created, email sent, item visible to the right people, total calculated correctly.)
- Does the happy path match the copy and UI the PRD describes, or has the build drifted from the spec?

Check specifically:

- **Every stated requirement is met.** Go line by line through the PRD's "must" and "should" statements. Each one is a pass/fail test.
- **Required fields** actually enforce. **Optional fields** actually work when left blank.
- **Navigation.** Every link and button in a core flow goes where it should. No dead ends, no 404s on internal routes.
- **State persistence.** Create something, reload, is it still there. Log out and back in, is it still there.
- **Permissions as intended for the normal case.** The user who should see a thing sees it. (Phase 4 handles whether the wrong user can.)
- **Numbers and calculations.** If the app computes anything (totals, counts, dates, splits), verify the math against a case you work out by hand.
- **Confirmations and feedback.** Does the user get told the action worked or failed? Silent success and silent failure are both findings.

A flow that works but does something different from what the PRD describes is a Medium finding at least. Note the gap between built behavior and intended behavior explicitly.
