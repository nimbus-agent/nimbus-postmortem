# Nimbus Postmortem

> **Retired and archived (2026-11-12).** This repository held a vision and a build prompt, and
> nothing was ever built here. The idea continues as gateway rows: *Post-mortem generation* in [Phase 10 of the Nimbus roadmap](https://github.com/nimbus-agent/Nimbus/blob/main/docs/roadmap.md#phase-10--the-autonomous-agent) and *Auto post-mortem* in [Phase 17](https://github.com/nimbus-agent/Nimbus/blob/main/docs/roadmap.md#phase-17--the-on-call-copilot).
> The text below is kept as it was written.

Draft an incident postmortem from [**Nimbus**](https://github.com/nimbus-agent/Nimbus)-indexed
incidents, alerts, deploys, and the changes implicated in them.

> **Status: SCAFFOLD — not yet built.** This repo holds its vision and a [build prompt](./NEW-SESSION-PROMPT.md).

## What it will be

Writing a postmortem is mostly archaeology: what fired, when, what shipped just
before it, which change was implicated, who was involved, how long it took to
recover. Nimbus has already indexed every one of those things — incidents and
alerts from PagerDuty / Datadog / Grafana / Sentry, deployments and DORA metrics
from the CI/CD data layer, and the pull requests, commits and people around
them, joined by a real relationship graph.

This tool assembles that into a **draft** a human then edits:

- **Timeline** — alerts firing, deploys landing, the incident opening and closing.
- **Implicated changes** — the deploys and pull requests inside the blast window,
  and how they connect to the incident through the graph (`resolves`,
  `mentions`, `correlates_with`).
- **Recovery** — time to acknowledge and MTTR, from the same DORA calculators
  the rest of Nimbus uses.
- **Participants** — who touched the incident, the deploys, and the fixes.

The output is a **draft, not a verdict.** It states what it can evidence and
leaves the analysis — root cause, contributing factors, actions — to the people
who were there. A postmortem that asserts a cause it cannot support is worse
than a blank page.

## Data sources & boundary

It reads the local gateway only: indexed `incident` / `alert` items, the
relationship graph, and the DORA surfaces. **It writes nothing back.** Publishing
a finished postmortem to a knowledge base is a separate, human-approved step —
Nimbus already gates knowledge-base writes behind the owner's consent
(invariant I25), and this tool deliberately stops short of it.

## License

[MIT](./LICENSE).
