# Report template

Copy this structure into `<project>_qa_<pass#>.md`. Fill it as you go, not at the end. Delete the guidance in parentheses.

---

# QA Strike Team Report — <project> — Pass <N>

**Date:** <date>
**Build / commit tested:** <commit hash or Lovable version>
**URL tested:** <running instance URL>
**PRD / spec used:** <path or source>

## Verdict

(One paragraph. Is this shippable? If not, what are the blockers? Count of findings by severity. Be blunt.)

Shippable: Yes / No / Not until blockers fixed
Critical: N · High: N · Medium: N · Low: N

## Findings

(Ordered by severity, Critical first. One block per finding.)

### [CRITICAL] Short title

- **Phase / persona:** <which phase, which persona if applicable>
- **Expected (per PRD):** <what should happen>
- **Actual:** <what happens>
- **Repro:**
  1. <exact step>
  2. <exact step>
  3. <what you see>
- **Fix:** <only if obvious, otherwise omit>

### [HIGH] Short title

(same structure)

### [MEDIUM] Short title

(same structure)

### [LOW] Short title

(same structure. For security findings, replace Repro with **File / line** and **Impact**.)

## Security ceiling

(The honest statement of what this review did not and could not cover, and what needs a human security review before real users and real data. Required. Do not skip it.)

## Coverage

(What you tested, briefly, so the reader knows what was and was not exercised. Flows walked, personas run, pages checked, what you did not get to.)

---

Keep titles specific. "Budget field accepts negative values and shows negative total" beats "input validation bug." The building agent should be able to find and fix each item from the title and repro alone.
