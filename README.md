# Implementation Scout

**Evidence before implementation.**

Turns "I need to implement X" (or "why is X happening in my system")
into a small, verified set of real GitHub citations — file, function,
version — plus an honest call on where a fix belongs versus adjacent
layers that look plausible but aren't it.

## The promise

Before AI writes the fix:

1. Reconstruct the actual workflow.
2. Find the real implementation.
3. Fetch the source.
4. Pin the version.
5. Prove provenance when possible.
6. Separate upstream facts from your deployment.
7. Mark VERIFIED / INFERRED / NOT VERIFIED.
8. Tell you CHANGE HERE / DO NOT CHANGE / DO NOT COPY.
9. Stop when the evidence stops.
10. Never invent implementation details.

## The killer behavior

The point isn't that Scout finds the right answer. It's that **Scout
is allowed to say "I don't have enough evidence."**

Across dozens of adversarial test rounds — three unrelated codebases
(Keycloak/Java identity federation, Grafana/Go+TypeScript RBAC,
PostgreSQL/C replication internals), three different models — the
single most valuable behavior wasn't a correct citation. It was a
model correctly downgrading a confident-sounding claim to
`NOT VERIFIED` because the evidence didn't actually support it.

## Install

**Claude.ai / Claude Code:** upload this repo (or its `SKILL.md`) as
a skill. See [Agent Skills docs](https://code.claude.com/docs/en/skills).

**Any platform supporting the Agent Skills / Skills standard:** point
it at `SKILL.md` in this repo.

## Usage

Ask an implementation or diagnostic question naturally — no special
invocation needed:

> "I need to implement idempotent webhook processing for Stripe
> events in Go."

> "Our Postgres replication slot keeps growing and filling disk —
> what's actually happening?"

Scout triggers automatically whenever answering well implies knowing
*where in real code* something happens, not just conceptually *that*
it happens. See `SKILL.md`'s execution gate for the exact trigger test.

## Known limitation

`PROVENANCE` and `INTEGRATION POINT` are the two evidence axes most
likely to get over-labeled `VERIFIED` on some models, even with the
skill's evidence rules in place — confirmed across three unrelated
domains, which points to a model tendency rather than a wording gap.
Treat a `VERIFIED` label on either axis as worth a quick independent
check. Full note in `SKILL.md`.

## Platform status

- **Claude** — tested extensively, ships.
- **ChatGPT** — tested, ships; consistently the strongest performer
  in adversarial testing (see `CHANGELOG.md`).
- **Gemini** — did not reliably execute the skill's evidence
  discipline in testing; not blocking launch, revisit later.

## Repository contents

- `SKILL.md` — the skill itself (model-facing instructions).
- `CHANGELOG.md` — the testing history: what broke, what fixed it,
  and why each rule exists.
- `LICENSE` — MIT.

## Next phase

Ship v1. Collect real failures from real usage. Modify the skill only
when a real failure justifies a change. The metric that matters: do
developers using this get measurably fewer hallucinated implementation
claims than they would from a plain "explain how to implement X"
prompt.
