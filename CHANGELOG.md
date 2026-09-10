# Changelog

Every entry here traces to a real, observed failure in adversarial
testing across Keycloak (Java/identity federation), Grafana
(Go+TypeScript/RBAC), and PostgreSQL (C/replication internals), run
against ChatGPT, Gemini, and Claude. Nothing was added speculatively —
each rule exists because a specific run produced a specific,
identifiable bad output without it.

## v1.0 — initial ship

### Core pipeline
- Workflow-first requirement extraction: search targets come from
  reconstructing actor → operation → component sequences, never from
  matching nouns/technologies in the request alone. Fixes: technically
  relevant-but-wrong-layer code (e.g. `keycloak-js`/`go-oidc`) being
  presented as the answer to a federation/token-exchange problem.
- Repository discovery, ranking, and source verification via real
  `api.github.com` + `web_fetch`/`web_search` calls — no citation
  permitted without a corresponding tool call in the same run.
- Narrowest-implementation-unit + semantic match requirement: prefer
  exact function/symbol over repo/module name, and require the match
  be justified by what the code does, not by keyword similarity.

### Hard scope boundary
- Scout finds implementations; it does not generate code, demo
  environments, or fictional credentials/realm names unless the user
  explicitly asks, as a separate follow-up. Fixes: a run that invented
  a full fictional Keycloak demo environment (realm names, test users,
  passwords) unprompted, and a separate run that generated two
  complete, unrequested application implementations (TypeScript +
  Java) for a request that only asked where a fix belonged.
- **Implementation gate**: even an explicit "implement X" request is
  gated on `RUNTIME PATH`, `INTEGRATION POINT`, and `APPLICABILITY`
  all being `VERIFIED` first — being asked for code is necessary but
  not sufficient. Fixes the specific loophole above: "implement X" was
  being treated as blanket permission regardless of whether anything
  about the user's actual system had been established.
- Never claim testing/execution occurred unless a tool call actually
  ran something. Fixes a run that asserted "Last Tested: <date>" and
  "production-ready" with no execution ever having happened.

### Evidence discipline
- `SOURCE` / `PROVENANCE` / `APPLICABILITY` (later extended to
  `VERSION`, `RUNTIME PATH`, `INTEGRATION POINT`) are six independently
  scored axes — a strong result on one must never "pull" another
  toward `VERIFIED`.
- Commit provenance requires the actual diff to be fetched and shown
  to introduce or materially change the cited implementation. Diff
  *context* (unchanged surrounding lines) is explicitly not provenance.
  No guessing commit SHAs or PR numbers, ever. Explicit stop condition
  and structured `STOP REASON` reporting when provenance can't be
  established — this is a successful outcome, not a failure.
- **Evidence-backed verdict rule**: a `VERIFIED` label requires a
  reconstructable proof object (`EVIDENCE ID` → current-run tool
  output → `EXACT OBSERVATION` → why it establishes the claim), not
  reasoning. Added after repeated observation that models would fill
  every axis with `VERIFIED` based on architectural plausibility once
  the six-axis format became mandatory — Goodhart's law on the format
  itself.
- Architectural decomposition for broad/incident-style requests
  ("RabbitMQ split-brain," not "implement X"): decompose into branches,
  never assume unstated deployment details, separate diagnosis from
  recovery, flag destructive operations explicitly, separate
  application semantics from orchestration layer.

### Execution and completion gates
- **Execution gate**: Scout is a workflow, not a persona — a model
  must not claim it was "loaded" or "activated" without having run the
  pipeline. Fixes a run that responded to a real implementation
  question with a description of its own capabilities and "how can I
  help?"
- **Trigger recognition**: questions phrased as "why is X happening"
  or "how does X work" about the user's own system are Scout triggers
  if answering well implies knowing *where* in real code to look — not
  exempted just because they're phrased as explanations rather than
  "implement X." Fixes a self-admitted case where an implementation
  question was treated as a normal architecture explanation and Scout
  was silently skipped entirely.
- **Blocking completion gate**: a literal checklist gate before any
  report ships, covering workflow reconstruction, real source fetched
  (not just documentation), provenance diff inspection, all six axes
  present and evidence-backed, and no offer-to-continue substituting
  for finishing the report.

### Tool capability
- Tool/API limitations are runtime facts to test each session, never
  hard-coded permanent rules — including limitations documented
  earlier in this very file. A token's absence doesn't mean evidence
  is unavailable; it means token-gated endpoints specifically may be.

## Known limitation (unresolved, documented rather than chased further)

`PROVENANCE` and `INTEGRATION POINT` are the two axes most likely to
be labeled `VERIFIED` without adequate evidence, on some models,
despite the evidence-backed verdict rule, the blocking completion
gate, and reminders placed directly at the point of labeling. This
held consistently across three unrelated domains (Keycloak, Grafana,
Postgres), which points to a model tendency rather than a wording gap
worth further iteration. Practical guidance: treat a `VERIFIED` label
on either axis as worth independent spot-checking, more than `SOURCE`
or `VERSION`. Confirmed to affect Claude and Gemini; ChatGPT was
consistently more reliable on this specific point throughout testing,
including correctly *rejecting* plausible-looking commit citations
when their diffs didn't actually support the claim.
