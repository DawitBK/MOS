# MOS: Manufacturing Operations Suite

A company-neutral ERP for running a manufacturing plant. The first customer is a flour mill. It has six modules:

| Module | In one line |
|---|---|
| Procurement | Suppliers, purchase requests and orders, and paying suppliers |
| Inventory | Stores, bins and silos, stock movements, counts |
| Lab and quality | Inspecting what arrives and what is produced |
| Gate | Trucks in and out, and the weighbridge |
| Production | Milling runs: wheat in, flour and bran out |
| Sales | Customer orders, deliveries, invoices and receipts |

> **Status (3 October 2026): planning only. No code has been written.** Building starts when the owner gives the go-ahead.
>
> **The plan: everything inside 26 weeks.** Build W1–W20, each module tested by the client's key users as soon as it is built, test server by W4, production server by W16, **go-live W24**, close support to W26 ([ADR-0025](docs/6-decisions/adr/ADR-0025-everything-in-six-months.md)).

---

## Easiest way in

**Open [`index.html`](index.html) in a browser.** It is a start page with every document as a card, showing what each one answers.

**To learn everything in the right order, follow the [reading guide](READING-GUIDE.md)** ([as a page](READING-GUIDE.html)): 20 stops, file by file, with a 1-hour fast route and the numbers to know before presenting.

## I want to…

| I want to… | Open |
|---|---|
| see **where things stand** | [PROGRESS.md](PROGRESS.md) |
| get **the whole picture on one page** | [Master plan](docs/1-overview/MOS-MASTER-PLAN.md) · [as a web page](docs/1-overview/MOS-MASTER-PLAN.html) |
| prepare for **the next client meeting** | [8-meetings](docs/8-meetings/): the 3 October [discussion pack](docs/8-meetings/2026-10-03-client-documents/DISCUSSION-PACK.html) and [owner briefing](docs/8-meetings/2026-10-03-client-documents/OWNER-BRIEFING.html) |
| know **what the system must do** | [Requirements (FRD)](docs/2-requirements/MOS-FRD.html) |
| see **who does what, step by step** | [Process model](docs/2-requirements/MOS-PROCESS-MODEL.md) · [as a web page](docs/2-requirements/MOS-PROCESS-MODEL.html) |
| see **where the data lives** | [Database design](docs/3-design/MOS-DATABASE-DESIGN.md) · [as a web page](docs/3-design/MOS-DATABASE-DESIGN.html) |
| see **how it will be built** | [Technical design (TDD)](docs/3-design/MOS-TDD.html) |
| see **the timeline and the effort** | [Implementation plan](docs/4-plan/MOS-IMPLEMENTATION-PLAN.html) · [Sprint plan](docs/4-plan/MOS-SPRINT-PLAN.html) · [Module 1 plan](docs/4-plan/MOS-PROCUREMENT-MODULE-PLAN.html) |
| see **what we send the client** | [Commercial proposal](docs/5-for-the-client/MOS-COMMERCIAL-PROPOSAL.html) · [Pricing and timeline](docs/5-for-the-client/MOS-PRICING-AND-TIMELINE.html) (fixed price ETB 986,615, by phase, sprint, module and feature) · [Questionnaire](docs/5-for-the-client/MOS-STAKEHOLDER-QUESTIONNAIRE.html) |
| see **what is decided and what is open** | [Decision register](docs/6-decisions/MOS-DECISION-REGISTER.html) · [Architecture decisions (ADRs)](docs/6-decisions/adr/) |
| read **our reviews** of the client's documents | [7-reviews](docs/7-reviews/), latest first: [client documents v2](docs/7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.html) |
| read **the client's own documents** | [9-inputs/from-client](docs/9-inputs/from-client/) |

## How the folder is organised

The folders are numbered in reading order: from the big picture down to the details, then the inputs.

```text
MOS/
├── index.html              start page (open in a browser)
├── README.md               this file
├── READING-GUIDE.md        the order to read everything in (+ .html)
├── PROGRESS.md             status, phases and the session log
├── CLAUDE.md               working rules for AI-assisted sessions
└── docs/
    ├── 1-overview/         the master plan: everything on one page
    ├── 2-requirements/     what the system must do; who does what, step by step
    ├── 3-design/           the database and the technical design
    ├── 4-plan/             estimate, timeline, sprints, the Module 1 plan
    ├── 5-for-the-client/   the commercial proposal and the questionnaire
    ├── 6-decisions/        the decision register and the ADRs
    ├── 7-reviews/          our reviews of the client's and the brainstorm documents
    ├── 8-meetings/         one folder per client meeting
    └── 9-inputs/           the client's documents, and the owner's ChatGPT brainstorm
```

**Every folder has a README** that says what is in it and which file to open first.

**Two formats, one content:**

- **`.md`** is the master copy. Read it in VS Code (Ctrl+Shift+V previews it) or on GitHub.
- **`.html`** is the same content as a page for the browser and for printing. Every main document has one.
- When the two differ, the `.md` wins.

Everything stays local. Nothing is published online.

## Relationship to other systems

MOS is **standalone**:

- **Sign-in:** it has its own local accounts. Any OpenID Connect identity provider can be added; the house SSO is the reference.
- **Documents:** it may archive documents to a DMS later.
- **Other systems:** it does not depend on, or share data with, any other system.
