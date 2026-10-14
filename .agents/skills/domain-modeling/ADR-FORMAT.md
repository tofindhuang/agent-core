# ADR Format

ADRs live in **module-scoped directories**, each with independent sequential numbering:

| Scope | Directory |
|---|---|
| Project-wide / cross-module | `docs/adrs/` |
| Gateway-only | `gateway/docs/adrs/` |
| Server-only | `server/docs/adrs/` |
| Web-only | `web/docs/adrs/` |

**Placement rule**: a decision affecting a single module → that module's `docs/adrs/`; cross-module/global (tech stack, orchestration, engineering practice, the ADR system itself) → `docs/adrs/`. When unsure, use the module directory — moving up is easy, moving down is rare.

## Template (mandatory sections)

```md
# {Short title of the decision}

Date: YYYY-MM-DD

## Status

{Accepted | Proposed | Rejected | Deprecated | Superseded by ADR-NNNN}

## Context

{The forces at play: technical, constraints, current state.}

## Decision

{What was decided, and briefly why.}

## Considered Options(被否的备选)

- **{Alternative}** — {one clause on what it is + why it was rejected}.

## Consequences

{Non-obvious downstream effects, follow-ups, risks.}
```

- **Status** and **Date** are mandatory.
- **Considered Options is mandatory** for new ADRs. A decision with no real alternative probably doesn't deserve an ADR; if it truly has none, write one line: 「无显著备选(顺理成章)」.
- **Rejected decisions get ADRs too** (Status=Rejected): recording what was beaten and why stops the next agent from re-litigating it.

## Numbering

Scan **your directory** for the highest existing number and increment by one. Cross-directory number collisions are legal (disambiguate by module context, e.g. `server ADR-0002`). On a branch collision within one directory: first-merged keeps the lower number; renumber before merging.

## When to offer an ADR

All three of these must be true:

1. **Hard to reverse** — the cost of changing mind later is meaningful
2. **Surprising without context** — a future reader will look at the code and wonder "why on earth did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If a decision is easy to reverse, skip it — you'll just reverse it. If it's not surprising, nobody will wonder why. If there was no real alternative, there's nothing to record beyond "we did the obvious thing."

### What qualifies

- **Architectural shape.** "We're using a monorepo." "Sessions persist in MySQL as a projection of gateway events."
- **Integration patterns between modules.** "Java never calls an LLM directly; the gateway is the only model path."
- **Technology choices that carry lock-in.** Database, message bus, auth provider, deployment target. Not every library — just the ones that would take a quarter to swap out.
- **Boundary and scope decisions.** The explicit no-s are as valuable as the yes-s.
- **Deliberate deviations from the obvious path.** These stop the next engineer from "fixing" something that was deliberate.
- **Constraints not visible in the code.** Compliance, latency budgets, provider limits.
- **Rejected alternatives when the rejection is non-obvious.** Otherwise someone will suggest the beaten option again in six months.
