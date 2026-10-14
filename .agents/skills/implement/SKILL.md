---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
---

Implement the work described by the user in the spec or tickets.

Use /tdd where possible, at pre-agreed seams.

Run the module's typecheck/build commands regularly (see `AGENTS.md` for the
per-module command map: `gateway` / `web` → `npm run typecheck`, `server` →
`mvn -q compile`), single test files regularly, and the module's full test
suite once at the end.

Once done, use /code-review to review the work.

Commit your work to the current branch following the commit format in
`AGENTS.md`.
