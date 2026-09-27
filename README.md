# MOS: Manufacturing Operations Suite

A company-neutral ERP for manufacturing plant operations. It has six modules:
- procurement (with supplier payment)
- inventory (multi-store, silos)
- raw-material testing and lab
- production
- sales
- gate management

It includes an operational payables/receivables sub-ledger that exports journals to external accounting.

> **Status: planning only.** This repository holds the planning package. No application code has been written.
> "MOS" is a working codename. The client's draft brands the sign-in page "Factory ERP".

**v0.3 framing (2026-09-27):**
- **Single company, single tenant**, with deliberate seams to grow into multi-company and SaaS later ([ADR-0017](docs/adr/ADR-0017-single-tenant-single-company-v1.md)).
- **One release: all six modules at core depth in 26 weeks, then 9 weeks to go live, on every team path** ([ADR-0018](docs/adr/ADR-0018-lean-v1-six-months.md), [ADR-0020](docs/adr/ADR-0020-six-modules-one-release-26-weeks.md)).
- **Stack locked:** Node 24, NestJS 12, PostgreSQL 18, BullMQ + Redis/Valkey, Next.js 16 ([ADR-0021](docs/adr/ADR-0021-stack-lock-in.md)); on-premises hosting first ([ADR-0022](docs/adr/ADR-0022-hosting-and-operations-baseline.md)).
- **Infrastructure is not assumed.** Hosting and deployment are decided after stakeholder answers ([ADR-0019](docs/adr/ADR-0019-infrastructure-not-assumed.md)).

## Start here
| Document | What it answers |
|---|---|
| [PROGRESS.md](PROGRESS.md) | **Where things stand:** the v0.3 phase programme and its status |
| [docs/MOS-MASTER-PLAN.md](docs/MOS-MASTER-PLAN.md) | The one-page view: decisions, scope, architecture, timeline, risks |
| [docs/MOS-FRD.md](docs/MOS-FRD.md) | **What** the system must do: requirement IDs, business rules, controls, NFRs, acceptance scenarios |
| [docs/MOS-PROCESS-MODEL.md](docs/MOS-PROCESS-MODEL.md) | **Who does what, in which order:** value chain, whole-ERP flow, swimlanes with numbered steps, lifecycles, events, exceptions, RACI, controls. Local printable view: `docs/MOS-PROCESS-MODEL.html` |
| [docs/MOS-DATABASE-DESIGN.md](docs/MOS-DATABASE-DESIGN.md) | **Where the data lives:** 230 tables in 9 schemas with every column and constraint, entity diagrams, worked examples, transaction write-sets, invariants, operations, ownership. Local printable view: `docs/MOS-DATABASE-DESIGN.html` |
| [docs/MOS-TDD.md](docs/MOS-TDD.md) | **How** it will be built: architecture, data model, ledger, inbound process, security, deployment options (design only) |
| [docs/MOS-IMPLEMENTATION-PLAN.md](docs/MOS-IMPLEMENTATION-PLAN.md) | **When and how much work:** the bottom-up estimate, capacity per team path, the week-level roadmap, go-live hours, risks |
| [docs/MOS-PROCUREMENT-MODULE-PLAN.md](docs/MOS-PROCUREMENT-MODULE-PLAN.md) | **Module 1 in detail:** stories, hours, week plans, UAT script, migration, training, go-live. Local view: `docs/MOS-PROCUREMENT-MODULE-PLAN.html` |
| [docs/MOS-COMMERCIAL-PROPOSAL.md](docs/MOS-COMMERCIAL-PROPOSAL.md) | **What it costs:** the client-facing proposal draft (teams, packages, modules, maintenance, billing, terms). Local view: `.html` |
| [docs/MOS-DECISION-REGISTER.md](docs/MOS-DECISION-REGISTER.md) | **What is decided and what is open**, for the owner and the client. Local view: `.html` |
| [docs/MOS-STAKEHOLDER-QUESTIONNAIRE.md](docs/MOS-STAKEHOLDER-QUESTIONNAIRE.md) | **The questions for the client**, with answer fields and needed-by weeks. Local view: `.html` |
| [docs/MOS-PACKAGE-AUDIT-v0.3.md](docs/MOS-PACKAGE-AUDIT-v0.3.md) | What the v0.3 audit found in v0.2, and how each finding was resolved |
| [docs/MOS-CLIENT-P2P-DRAFT-REVIEW.md](docs/MOS-CLIENT-P2P-DRAFT-REVIEW.md) | What the client's procure-to-pay draft changed: screen mapping, decisions, issues to fix, questions |
| [docs/client-input/](docs/client-input/) | The client's own documents, as received |
| [docs/reference-input/owner-brainstorm/](docs/reference-input/owner-brainstorm/) | The owner's ChatGPT brainstorm outputs (secondary reference; **not** client documents), reviewed in [docs/MOS-BRAINSTORM-OUTPUT-REVIEW.md](docs/MOS-BRAINSTORM-OUTPUT-REVIEW.md) and superseded by the process model |
| [docs/adr/](docs/adr/) | **Why:** architecture decision records |
| [docs/MOS-MASTER-PLAN.html](docs/MOS-MASTER-PLAN.html) | Browsable HTML view of the package. It is **local only**; open it in a browser |
| [CLAUDE.md](CLAUDE.md) | Conventions for AI-assisted sessions |

## Relationship to other systems
MOS is **standalone**:
- It can use any OpenID Connect identity provider; an existing SSO is the reference IdP for development. Local accounts are the default.
- It may archive documents to a DMS later.
- It does not depend on, or share data with, any other system.
