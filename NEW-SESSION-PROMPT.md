# Build prompt — `nimbus-postmortem`

> **Historical.** This build prompt is kept as written. The repository was retired on 2026-11-12;
> nothing should be built from it. See the note at the top of `README.md`.

Copy everything below into a fresh Claude Code session opened in a clone of `nimbus-agent/nimbus-postmortem`, with the main `nimbus-agent/Nimbus` repo available locally for reference.

---

I'm building **`nimbus-postmortem`**, a generator that drafts an incident postmortem from Nimbus-indexed incidents, alerts, deploys and the changes implicated in them — read-only, from the local gateway.

**Read first in the main Nimbus repo so the data access is correct:**

- The "Monitoring and Incidents" section of `docs/architecture.md` — the `incident` / `alert` item types and their indexed fields.
- `packages/gateway/src/metrics/` — the DORA calculators, in particular MTTR and how a P1 incident is counted (`severity_p1_aliases`).
- The skill `.claude/commands/nimbus-cicd-data-layer.md` — the `/v1/metrics/dora` surface, deployment annotation, and the `[metrics.dora.<id>]` / `[ci.service.<id>]` config that binds a repo to a service.
- `packages/gateway/src/agents/why.ts` and the skill `.claude/commands/nimbus-agent-patterns.md` — the six-lane provenance agent. It already answers "why does this exist" over the relationship graph; a postmortem timeline is a close cousin, and its lane decomposition is worth reusing rather than reinventing.
- The graph relation types (`resolves`, `mentions`, `correlates_with`) — how an incident connects to the PRs and deploys around it.
- The skill `.claude/commands/nimbus-http-write-surface.md` — confirms the HTTP API is read-only except a deploy carve-out you will not use.

**Goal:** given an incident reference, produce a correct, evidence-only postmortem draft from a populated local index.

**Deliverables:**

1. A generator that takes an incident (id or URN) and reads, via the gateway's IPC or read-only HTTP API: the incident and its alerts, deployments inside the blast window, PRs/commits connected through the graph, participants, and MTTR.
2. A templated **markdown draft**: timeline, implicated changes, recovery metrics, participants.
3. Config for the blast window and which services/repos are in scope.
4. `--json` output alongside the markdown, matching the convention used by `nimbus security scan`.

**Acceptance criteria:**

- Against a populated local index, the draft's timeline is correct and every claim traces to an indexed item — no inferred causes.
- It writes **nothing** back to the gateway, and never publishes to a knowledge base.
- It degrades gracefully: an incident with no linked deploys, or a null MTTR, produces a valid draft that says so rather than omitting the section or inventing a value.
- Absence of evidence is stated as absence, never rendered as a confident empty result.

**The one thing to get right:** this tool must not assert a root cause. It reports what is evidenced — what fired, what shipped, what connects — and leaves analysis to the humans who were there. A postmortem that confidently names the wrong cause is worse than no draft at all, because it anchors the review.

**Process:** brainstorm → spec → plan first. Confirm the incident/alert read methods and the DORA schema against the real code before building — the roadmap and the code have disagreed before. Keep it strictly read-only. Verify against a running gateway with sample data before opening the PR. License is MIT.
