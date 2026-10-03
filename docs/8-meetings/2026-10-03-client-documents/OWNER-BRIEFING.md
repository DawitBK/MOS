# Owner briefing for the 3 October meeting (for you only, not for the client)

<!-- nav -->[MOS](../../../README.md) › [8. Meetings](../README.md) › [3 Oct meeting](README.md)

**Meeting:** Saturday 3 October 2026, "1 o'clock at night" local time, which is **7 PM**. That is how the chat on 2 October settled it: you proposed "1 pm local" and the client asked "1 night?"; you said yes.

**What the client said they will discuss:** "the two documents mentioned above". Those are the two sent on 1 October as "final": the **procure-to-pay UI** (P2P process v2) and the **data dictionary**. The architecture proposal came separately on 2 October, so it is in the pack's appendix in case it comes up.

**What to share on screen:** `DISCUSSION-PACK.html`. It is written for the client: plain language, no internal decision IDs, no prices.

---

## 1. Your goal for the meeting, in three lines

1. **Agree:** "we capture every field and every step; the table design is ours" (Q-18).
2. **Get answers, or an owner and a date, for the four questions that size the work** (Q-01 to Q-04). They decide how much fits in the six-month plan.
3. **Leave with the next steps agreed:** their answers within a week; a third revision of page 1; a realistic example for acceptance testing.

## 2. Open with this

> "Thank you for these documents. They are detailed and they confirm that your process and ours are the same. Almost every one of your screens matches what we have planned, and we're adding the three that are new. We'll take every field in your data dictionary. Today we'd like to go through the points to agree, and four questions only you can answer, because they decide the size of the work."

Keep the tone **"we're adopting your work"**, not "we found 25 defects". A second, page-by-page check on 3 October corrected our own review in a few places (§3.6, §4.5 and §5.4 of the review); the pack already uses the corrected facts. The full internal audit is [MOS-CLIENT-DOCS-V2-REVIEW.md](../../7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md); don't show it to the client as it is.

## 3. Positions to hold, and why (if they push back)

| Topic | Hold this | One-line reason to give |
|---|---|---|
| Table design | Ours, with every client field added | Without line links the system can't stop paying the same delivery twice; an editable stock history corrupts balances |
| Status dropdowns | No: status changes only by actions (submit, approve, post) | A dropdown lets anyone skip the approval |
| Supplier bank details | Only from the approved supplier record; a change needs a second person | One of the most common payment-fraud routes |
| Supplier-raised payment requests (page 22) | Not in this release; a priced change later | It contradicts their own page 1, and opens the system to outsiders |
| Rejected delivery received in full (page 9) | Never: rejected stock is returned or held | Otherwise rejected grain becomes payable stock |
| One costing method per company | Yes, not per store or silo | The same wheat can't have two costs |
| Posted count = approved count | Yes | Otherwise the count can be quietly changed at posting |
| Different people for recording, inspecting and receiving a delivery | Yes, with logged exceptions for a small team (Q-23) | The examples show one person in all three roles; nobody would check their work |
| TypeScript | Keep it | Their examples are JavaScript, but they call TypeScript "worth considering". It catches mistakes before users see them, and JavaScript developers pick it up quickly |

## 4. Where you can give ground at no cost

- **Their module names** in our offer (procurement, inventory, QC, payables, production, sales, receivables, user management), with the gate shown separately.
- **Their folder names** inside each module, so their developers feel at home.
- **React Router, and a Vite build** for the frontend, which their architecture proposes. **This is still your open decision O-27**: I recommend Vite. If you haven't decided, say "we're evaluating it and lean towards your choice".
- **No Redis**: pg-boss inside PostgreSQL. Your open decision O-28; I recommend pg-boss. Same line if undecided.
- **OpenAPI/Swagger documentation** from the start (about 8 hours).
- **"Approve every invoice"** as a setting (Q-04).
- **Which release each addition lands in.** The pack says some additions come in the follow-up release. The six-month re-plan (done, 3 October) keeps blind counting, the Excel count import, "checked by" and invoice approval at standard depth (built if time allows), and makes finished-flour release and the stock check core.

**One default to know about:** Q-20's default assumes that finished-flour release and the stock check before sales-order approval are at go-live depth. That is my recommendation, about +16 hours: without the release, finished flour stays on hold and can never be sold. If you disagree, change the default before the meeting.

## 5. If they ask about the timeline

Your instruction (3 October) is that **everything fits in under six months**: build, testing, hosting, UAT, training and go-live. **The re-plan is done** ([ADR-0025](../../6-decisions/adr/ADR-0025-everything-in-six-months.md), [implementation plan](../../4-plan/MOS-IMPLEMENTATION-PLAN.md) 0.5). What to say:

> "Everything, including testing on your servers, your team's acceptance testing, training and going live, is planned inside six months, with go-live in week 24. Each module goes to your key users for testing as soon as it's built, rather than all at the end. Your answers on weighing and grading today decide how comfortable that is."

| Weeks | What |
|---|---|
| W1–W20 | Build, in two-week sprints; each module tested by their key users as it lands (from about W8) |
| By W4 / W16 | Test server on their infrastructure / production server ready |
| W21–W22 | Full system test and final acceptance test |
| W23 | Training and the data-migration rehearsal |
| **W24** | **Go-live** |
| W25–W26 | Hypercare (close support right after go-live), then maintenance |

**What the re-plan found (for you, not for the client):**
- The speed needed to build everything in 20 weeks: **path A+ (3 developers + QA) ×1.33**, the best case of the estimate; **path A (2 developers + QA) ×1.89**; path B ×2.22; path C does not fit. Recommendation: lead with A+ (open decision O-34).
- **"No gate module" and "pass/fail inspection" remove about 230 core hours:** A+ drops to ×1.19 and A to ×1.59. That is the difference between a comfortable and a tight six months, so Q-01 and Q-02 matter most tonight.
- Two things the client must give **before W1**: the infrastructure answers (the test server is needed by W4) and the names of their key users.

## 6. If they ask about price

**Your instruction (3 October): the price stays within ETB 1,000,000.** The proposal draft now carries **one fixed price of ETB 986,615** (about US$6,090) for every given requirement and every go-live service; maintenance, new features, hosting and third-party services are outside it. The breakdown by phase, sprint, module and feature is in the [pricing and timeline](../../5-for-the-client/MOS-PRICING-AND-TIMELINE.md). If asked, you can say: "about ETB 990,000, fixed, everything to go-live included, paid as each part is accepted."

**For you only:** at the US$5 rate card the six-month team costs ETB 3.59 million (A+), 2.82 million (A) or 2.17 million (B). The fixed price covers about 27% of A+'s cost, about ETB 222 per team hour instead of ETB 810. That is your call (register A-43), but go in knowing it.

## 7. Things to note during the meeting

- The answer to each Q number. The pack's tables have a column for it in the printable view.
- Who answers what, and by when.
- **Who the key users are** per module (needed for acceptance testing).
- **Whether they have a weighbridge**, its make and model, and whether it connects to a PC.
- **Their IT set-up**, if it comes up: servers, Windows or Linux, internet at the gate. The infrastructure questionnaire (I-01 to I-17) covers it.

## 8. Where each question lives in our register

The pack numbers questions Q-01…Q-23 for the client. This is where each one goes in the [decision register](../../6-decisions/MOS-DECISION-REGISTER.md) and the [review](../../7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md) §8. C-01…C-49 and I-18 are all in the register (0.2, updated 3 October with the review's questions). **C-48, C-49 and I-18 are new from the 3 October re-check.**

| Pack | Register | Pack | Register | Pack | Register |
|---|---|---|---|---|---|
| Q-01 | C-38 (+ C-29) | Q-09 | C-04 | Q-17 | C-16, C-17 |
| Q-02 | C-25…C-27 | Q-10 | C-41 | Q-18 | C-37 (O-33) |
| Q-03 | C-39 | Q-11 | C-43 | Q-19 | C-24 |
| Q-04 | C-42 | Q-12 | C-44 | Q-20 | **C-48** |
| Q-05 | C-40 | Q-13 | C-28 | Q-21 | **I-18** |
| Q-06 | C-37 | Q-14 | C-45 | Q-22 | C-24 (updated) |
| Q-07 | C-36 | Q-15 | C-46 | Q-23 | **C-49** |
| Q-08 | C-03 | Q-16 | C-47 | | |

## 9. After the meeting

Send me your notes, or just the answers, and I will:
1. fold them into the requirements, the database design and the register;
2. update the questionnaire;
3. recompute the six-month plan with the answers (the model is set up for it: Q-01/Q-02 alone move A+ from ×1.33 to ×1.19).
