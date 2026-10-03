# MOS reading guide: from zero to presentation-ready

<!-- nav -->[MOS](README.md) › Reading guide

| | |
|---|---|
| What this is | The order to read the MOS folder in, file by file, with what to look at in each and how long it takes |
| For | The owner, before presenting, discussing or deciding |
| Two routes | **Fast route** (about 1 hour, stops marked ★) · **Full route** (about 7 hours, every stop) |
| How to open the files | Open each **page** (`.html`) in a browser; it has tabs and prints cleanly. The `.md` next to it is the same text, best read in VS Code (Ctrl+Shift+V) |
| Date | 2026-10-03 |

---

## The route at a glance

| Part | What you will know at the end | Stops | Time |
|---|---|---|---|
| **A. Orientation** | What MOS is, where everything is, where things stand | 1–4 | 25 min |
| **B. The client** | What the client sent, what we found, what we asked them | 5–8 | 75 min |
| **C. What the system does** | Every requirement, every process step, every table (at a glance) | 9–11 | 90 min |
| **D. How it is built** | The architecture and the big decisions behind it | 12–13 | 40 min |
| **E. Time and money** | The six-month plan, the team, the sprints, the fixed price | 14–18 | 100 min |
| **F. Decisions and questions** | What is decided, what you still decide, what the client must answer | 19–20 | 40 min |
| **G. Ready to present** | The numbers to know, the questions you will get, and the page that answers each | the checklist below | 15 min |

---

## A. Orientation (25 min)

| # | Open | Folder | Look at | Time | You're done when you can say… |
|---|---|---|---|---|---|
| **1** ★ | [index.html](index.html) | `MOS/` | The status strip at the top, then scroll the cards once. Don't open anything yet | 3 min | "There are nine numbered folders, from overview to inputs." |
| **2** ★ | [README.md](README.md) | `MOS/` | The module table, the status note, the "I want to…" table | 5 min | "MOS is six modules for a flour mill, planning only, go-live W24." |
| **3** | [PROGRESS.md](PROGRESS.md) | `MOS/` | Only the **Overall status** paragraph and the newest session-log entries at the bottom | 5 min | "Planning is complete; implementation waits for my go-ahead." |
| **4** ★ | [Master plan](docs/1-overview/MOS-MASTER-PLAN.html) | `docs/1-overview/` | Tabs **In one minute** and **Plan and teams**. Skim **Decisions and design** | 12 min | "Everything in 26 weeks; recommended team A+ (3 developers + QA); fixed price ETB 986,615." |

## B. The client: what they sent, what we found, what we asked (75 min)

| # | Open | Folder | Look at | Time | You're done when you can say… |
|---|---|---|---|---|---|
| **5** | [The client's documents](docs/9-inputs/from-client/README.md) | `docs/9-inputs/from-client/` | The README's table of four documents. Then open **P2P-process-v2.pdf** and page through the 28 screens quickly | 15 min | "Their P2P v2 has 28 screens; they also sent a data dictionary and an architecture proposal." |
| **6** | [Client documents v2 review](docs/7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.html) | `docs/7-reviews/` | Tab **Summary** (§1–2), then **Integration plan** (§7.3 effort, §7.5 your decisions). Skim the other tabs for the headings only | 20 min | "We adopt every field and three new screens; it adds 59 core hours; seven of my decisions came out of it." |
| **7** ★ | [Discussion pack, 3 Oct](docs/8-meetings/2026-10-03-client-documents/DISCUSSION-PACK.html) | `docs/8-meetings/2026-10-03-client-documents/` | The agenda, §4 (the four questions that size the work, Q-01–Q-04) and §6 next steps. This is what the client sees | 20 min | "The four questions are: gate and weighing, grading, who raises payment requests, invoice approval." |
| **8** ★ | [Owner briefing, 3 Oct](docs/8-meetings/2026-10-03-client-documents/OWNER-BRIEFING.html) | same folder | §3 positions to hold, §4 where to give ground, §5 timeline, §6 price. **For you only** | 20 min | "I know what to hold firm on, what to concede, and what not to promise." |

## C. What the system does (90 min)

| # | Open | Folder | Look at | Time | You're done when you can say… |
|---|---|---|---|---|---|
| **9** | [Requirements (FRD)](docs/2-requirements/MOS-FRD.html) | `docs/2-requirements/` | Tab **Scope and roles** (§1–2). Then in **Inventory, procurement, QC, gate** read §9, the inbound process end to end. Then **Reports, NFRs, controls, scenarios**: §15 controls and §16 the acceptance scenarios | 40 min | "231 requirements, 205 in v1; the headline is the inbound loop; 19 scenarios prove it works." |
| **10** | [Process model](docs/2-requirements/MOS-PROCESS-MODEL.html) | `docs/2-requirements/` | Tab **Overview**: the value chain and the whole-ERP flow with its 33 hand-offs. Then in **Processes** open the inbound-delivery swimlane | 30 min | "I can walk someone through truck → weigh → lab → GRN → payment, and say who does each step." |
| **11** | [Database design](docs/3-design/MOS-DATABASE-DESIGN.html) | `docs/3-design/` | Tab **Overview** only: the schema map and the conventions. The rest is reference for developers | 20 min | "230 tables in 9 groups; posted records can never be edited, only reversed." |

## D. How it is built (40 min)

| # | Open | Folder | Look at | Time | You're done when you can say… |
|---|---|---|---|---|---|
| **12** | [Technical design (TDD)](docs/3-design/MOS-TDD.html) | `docs/3-design/` | Tab **Architecture and stack** (§1–2, §4). Skim **Identity, transactions, posting** | 25 min | "A modular monolith on Node 24, NestJS and PostgreSQL 18, one installation per company, on the client's own server." |
| **13** | [Architecture decisions (ADRs)](docs/6-decisions/adr/README.md) | `docs/6-decisions/adr/` | The index table. Then open **ADR-0025** (everything in six months) and **ADR-0021** (the stack) | 15 min | "Every big technical choice has a dated record with its reason; 0025 is the six-month calendar." |

## E. Time and money (100 min)

| # | Open | Folder | Look at | Time | You're done when you can say… |
|---|---|---|---|---|---|
| **14** ★ | [Implementation plan](docs/4-plan/MOS-IMPLEMENTATION-PLAN.html) | `docs/4-plan/` | Tab **Summary** (the glossary, the calendar, the team table), then **Capacity and speed** (§5: why A+ needs ×1.33 and A ×1.89), then **Go-live and inputs** (§8, §9) and **Governance and risks** (§13) | 35 min | "A+ needs the best case of the estimate; A only works without the gate; C doesn't fit. The client's infrastructure answers are needed before W1." |
| **15** | [Sprint plan](docs/4-plan/MOS-SPRINT-PLAN.html) | `docs/4-plan/` | Tab **Overview**: §2 the sprint calendar and §3.3 the five milestone reviews. Then one sprint in **Sprints** (S8, the procure-to-pay review) to see the level of detail | 20 min | "Thirteen sprints; M4 in W16 shows the client's procure-to-pay end to end." |
| **16** | [Module 1 plan (procurement)](docs/4-plan/MOS-PROCUREMENT-MODULE-PLAN.html) | `docs/4-plan/` | Tab **Overview** (the timeline table), then **Acceptance and UAT** (the 22 UAT cases) | 15 min | "54 stories; the client's key users test procure-to-pay with these 22 cases." |
| **17** ★ | [Pricing and timeline](docs/5-for-the-client/MOS-PRICING-AND-TIMELINE.html) | `docs/5-for-the-client/` | §1 summary, §2 by phase, §3 by module, §8 payment schedule. §4–§5 (by sprint and by feature) are for when someone asks "what does X cost?" | 20 min | "ETB 986,615, fixed, within ETB 1,000,000; 10% at signature, then paid per accepted review." |
| **18** | [Commercial proposal](docs/5-for-the-client/MOS-COMMERCIAL-PROPOSAL.html) | `docs/5-for-the-client/` | Read it as the client would, start to finish. Note every **[owner to confirm]** | 10 min | "I know which terms I still have to confirm before it is sent." |

## F. Decisions and questions (40 min)

| # | Open | Folder | Look at | Time | You're done when you can say… |
|---|---|---|---|---|---|
| **19** ★ | [Decision register](docs/6-decisions/MOS-DECISION-REGISTER.html) | `docs/6-decisions/` | Tab **B. Owner** first: your open decisions (O-34 team, O-27/O-28 stack, O-26 extra capacity). Then **A. Decided**, rows A-42 (six months) and A-43 (fixed price, with the internal cost note) | 25 min | "My three most urgent decisions are O-34, O-27 and O-28." |
| **20** | [Stakeholder questionnaire](docs/5-for-the-client/MOS-STAKEHOLDER-QUESTIONNAIRE.html) | `docs/5-for-the-client/` | Section 10 (infrastructure) and section 12 (questions from their October documents). Everything marked "Before W1" | 15 min | "Before we start, the client owes: the four sizing answers, the infrastructure for the test server, and the key users." |

**Optional, history only** (read when someone cites them): the [first-draft review](docs/7-reviews/MOS-CLIENT-P2P-DRAFT-REVIEW.html), the [ChatGPT brainstorm review](docs/7-reviews/MOS-BRAINSTORM-OUTPUT-REVIEW.html), the [v0.3 package audit](docs/7-reviews/MOS-PACKAGE-AUDIT-v0.3.html), the [owner brainstorm](docs/9-inputs/owner-brainstorm/README.md), and ADR-0001…0016.

---

## G. Ready to present

### The numbers to know by heart

| Number | What it is | Where it comes from |
|---|---|---|
| **6** modules | Procurement (with supplier payment), inventory, lab and quality, gate and weighbridge, production, sales (with receivables) | Master plan |
| **26 weeks**, go-live **W24** | Everything included: build W1–W20, system test and UAT W21–W22, training W23, go-live W24, hypercare W25–W26 | ADR-0025 |
| **W4 / W16** | Test server on the client's infrastructure / production server ready | Implementation plan §8 |
| **231 / 205** | Requirements in total / in v1 | FRD |
| **2,439 h** core · **3,177 h** total | Estimated work; the client's documents added 59 core hours | Implementation plan §3 |
| **×1.33 / ×1.89** | Speed needed: path A+ (3 developers + QA) / path A (2 + QA). Without the gate module: ×1.19 / ×1.59 | Implementation plan §5 |
| **ETB 986,615** | The fixed price (within ETB 1,000,000), every given requirement and every go-live service | Pricing and timeline |
| **10%** | At signature; then each review pays for what it accepted | Pricing and timeline §8 |
| **W4 / W8** | The two speed checkpoints; capacity is added before W9 if behind | Implementation plan §11 |
| **Q-01–Q-04** | The four client answers that size the work | Discussion pack §4 |

### The questions you will get, and the page that answers each

| If someone asks… | Open | Where |
|---|---|---|
| "Can you really do all of it in six months?" | Implementation plan | §5 (speed per team, the honest verdicts) and §11 (checkpoints) |
| "When do we see something working?" | Sprint plan | §3.3, the five reviews (M1 in W4 on the test server) |
| "When do our people test?" | Implementation plan | §8.1: rolling UAT from about W8, final UAT W21–W22 |
| "What exactly does it cost, and what isn't included?" | Pricing and timeline | §1 and §9 |
| "What does module X or feature Y cost?" | Pricing and timeline | §3 (modules), §5 (features) |
| "When do we pay?" | Pricing and timeline | §8 |
| "What do you need from us, and by when?" | Implementation plan / questionnaire | §9 / the "Needed by" column |
| "Why not our table design (the data dictionary)?" | Owner briefing / v2 review | §3 (positions to hold) / §4 |
| "Why TypeScript, NestJS, PostgreSQL?" | TDD / ADR-0021 | §2 |
| "Where does it run? Do we need the cloud?" | Master plan | §4 (on-premises first; infrastructure at cost) |
| "What if we don't need the gate module?" | Implementation plan | §5.3 (about 190 hours less; speed needed drops) |
| "Who owns the code?" | Commercial proposal | §13 (the client owns its build) |

### Before you walk in

- [ ] Read the fast route (★) at least.
- [ ] Decide or prepare a line on **O-34** (which team), **O-27/O-28** (Vite, pg-boss) and **O-26** (who pays for added capacity).
- [ ] Confirm or adjust the **[owner to confirm]** items in the proposal.
- [ ] Have the [discussion pack](docs/8-meetings/2026-10-03-client-documents/DISCUSSION-PACK.html) open to share and the [owner briefing](docs/8-meetings/2026-10-03-client-documents/OWNER-BRIEFING.html) open for yourself.
- [ ] Know the internal cost note under A-43 in the register: the fixed price covers about 27% of path A+'s team cost at the rate card.
