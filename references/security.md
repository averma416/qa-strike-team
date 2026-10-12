# Phase 4 — Security review

Run this as an **independent subagent** (the `Agent` tool, `general-purpose`). Give it the repo and the app's purpose, and tell it to review against this checklist and return findings with file and line. Keep it independent from the testing above: a reviewer that has not been defending the code finds more.

This is a source and configuration review plus checks against the running app. It is not exploitation. Do not craft live attacks against third-party services or anything destructive. Review, identify, report.

## First, for any Supabase / client-direct-to-database stack

This stack (Lovable, Supabase, Firebase, anything where the browser talks to the database with a public key) has one dominant risk, so check it first and hardest:

- **Row Level Security.** Is RLS enabled on every table holding user data? A table with RLS off is wide open to anyone with the public key. For each table, is there a policy, and does the policy actually restrict rows to the owner or authorized users? A policy of `true` is the same as no policy. This is the single most common critical hole in this stack. Verify against the actual schema and policies, not the intent.
- **The anon/public key is meant to be public, but it is only safe if RLS is correct.** Confirm the code is not relying on client-side checks to protect data the key can reach.
- **Service-role key never in client code.** Search the client bundle and repo for the service key. If it is reachable from the browser, that is Critical.
- **Storage bucket policies.** Same logic as RLS, for files. Public buckets holding private files are a finding.

## For every app

**Secrets and keys**
- No API keys, tokens, passwords, or connection strings committed to the repo or shipped in the client bundle. Check `.env` handling, hardcoded strings, and what ends up in the built JS.
- `.env` and secrets are gitignored and not in history.

**Authentication**
- Auth actually gates protected routes, server-side, not just by hiding the UI. A protected page reachable by typing its URL is a finding.
- Session handling: tokens stored sensibly, sessions expire, logout actually invalidates.
- Password rules if the app rolls its own auth. Prefer a managed provider; flag hand-rolled auth for extra scrutiny.

**Authorization (the big one after RLS)**
- **Insecure direct object references.** Can user A load user B's record by changing an ID in the URL or request? This is the most common real-world breach. Test it against the running app with two accounts if you can.
- Every privileged action checks the actor's permission server-side.
- No "hidden" admin routes protected only by obscurity.

**Input handling**
- Server-side validation on everything, not just client-side. Client validation is UX, not security.
- Injection surfaces: any raw SQL built from user input, any `dangerouslySetInnerHTML` or equivalent, any user input rendered without escaping (XSS), any user input in a shell or eval path.
- File uploads: type and size checked server-side, files not executable, stored where they cannot be served as code.

**Transport and config**
- HTTPS everywhere. No mixed content.
- CORS not set to `*` on anything authenticated.
- Security headers present (CSP, HSTS) for a production app.
- Error messages do not leak stack traces, queries, or internal paths to users.

**Dependencies**
- Run the audit tool for the stack (`npm audit`, etc.) and report known-vulnerable packages, highest severity first.

## Reporting this phase

Each finding: severity, the vulnerability class, file and line (or the table/policy/config for infra issues), what an attacker could do, and the fix.

Then, the ceiling statement. End the security section by naming plainly what this review cannot guarantee and what needs a human security review before real users and real data: anything handling payments, anything regulated (health, finance, minors' data), hand-rolled crypto or auth, and anything where the blast radius of a miss is large. Do not imply the app is secure because this pass found nothing. State that this is a review, not a penetration test.
