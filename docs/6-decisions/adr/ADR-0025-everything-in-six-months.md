# ADR-0025: Everything inside six months: build, testing, servers, UAT, training, go-live and hypercare

<!-- nav -->[MOS](../../../README.md) › [6. Decisions](../README.md) › [ADRs](README.md)

- **Status:** Accepted (planning), 2026-10-03. **Amends [ADR-0020](ADR-0020-six-modules-one-release-26-weeks.md)**: the scope stays the same (all six modules at core depth, one release), but the calendar changes from "26 weeks of build + 9 weeks to go live" to **26 weeks for everything**. Also amends [ADR-0018](ADR-0018-lean-v1-six-months.md) §3 and [ADR-0019](ADR-0019-infrastructure-not-assumed.md) (the hosting decision moves to before W1).
- **Number:** ADR-0023 and ADR-0024 stay reserved for the stack questions O-27 (frontend) and O-28 (job queue).
- **Trigger:** the owner's instruction of 2026-10-03: *"make it so that everything including deployment, testing, hosting, UAT etc. fits in under 6 months"*.

## Context

- **The previous calendar (ADR-0020)** was 35 weeks: build W1–W26, then a separate go-live window W27–W35 (system test, production set-up, UAT, migration, training, cutover, three weeks of hypercare).
- **The work did not shrink.** After the client-documents review (2026-10-03) the core is **2,439 estimated hours** (it was 2,380; [review §7.3](../../7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md)).
- **Going live takes calendar time that AI does not shorten:** the client's key users must test, the servers must exist, people must be trained. So the go-live work cannot simply be squeezed into the last week.

## Decision

1. **One 26-week calendar for everything:**

   | Weeks | What happens | In plain words |
   |---|---|---|
   | W1–W20 | **Build**, in two-week sprints; hardening and feature freeze in W20 | The team writes the system, module by module |
   | from about W8 | **Rolling UAT** | Each module goes to the client's key users as soon as it is built, so testing doesn't pile up at the end |
   | by W4 | **Test server** on the client's infrastructure | The client sees and tests the real system on their own equipment from month 1 |
   | by W16 | **Production server** set up, secured, backed up and restore-tested | The live server is ready well before go-live |
   | W17–W20 | Data migration: mapping and first dry run | Old data (suppliers, items, stock, balances) is prepared early |
   | W21–W22 | **System test and final UAT**: every scenario end to end; sign-off | The last full check of the whole flow |
   | W23 | **Training** and the migration rehearsal | Every user trained; a full practice run of the data load |
   | W24 | **Go-live** (cutover) | The client starts working in the system |
   | W25–W26 | **Hypercare**, then maintenance | Close daily support right after go-live |

2. **The scope is unchanged:** all six modules at core depth in one release. Standard and full depth stay the pull-in list (built only if the team runs ahead), otherwise v1.1.
3. **Velocity checkpoints move earlier, to W4 and W8.** If the team is more than 10% behind the speed its path needs, capacity is added **before W9**. The date and the core scope do not move. *(Velocity = how fast the team finishes the estimated work. ×1.33 means each estimated hour is done in 45 minutes.)*
4. **Each team path is planned at the speed it needs** ([implementation plan](../../4-plan/MOS-IMPLEMENTATION-PLAN.md) §5):

   | Path | Team | Speed needed | Each estimated hour done in | Verdict |
   |---|---|---|---|---|
   | **A+** | 3 developers + AI + QA | **×1.33** | 45 minutes | Tight: at the best case of the estimate range |
   | **A** | 2 developers + AI + QA | **×1.89** | 32 minutes | Very tight: beyond the best case |
   | **B** | 2 developers + AI | **×2.22** | 27 minutes | Very tight: beyond the best case |
   | **C** | 1 developer with heavy AI | ×4.17 | 14 minutes | **Does not fit**: the go-live work alone needs 10.7 person-weeks in a 6-week window |

   If the client drops the gate module and uses pass/fail inspection (pack questions Q-01 and Q-02), path A+ needs ×1.19 and path A ×1.59.
5. **Which paths are offered to the client is the owner's decision O-34.** Recommendation: **lead with A+**; offer A only together with the gate and grading answers; don't offer B or C for the six-month calendar.
6. **The hosting decision moves to before W1**, because the test server is needed by W4. The production-server questions are needed before W12.

## Consequences

- **For the client:** all six modules live in **week 24**, about five and a half months after the go-ahead, with hypercare finished by week 26. Their key users test each module from about week 8, not only at the end.
- **For the team:** a shorter build (20 weeks instead of 26) and a much higher required speed on two-developer paths. Path A rises from ×1.32 to ×1.89. Recorded as risks R17, R22 and R25 in the implementation plan.
- **For the price:** the team's cost at the US$5 rate card follows its weeks (A+ $22,190, A $17,390, B $13,390). The client price was then fixed by the owner at **ETB 986,615**, within ETB 1,000,000, for every given requirement and every go-live service (register A-43; [pricing and timeline](../../5-for-the-client/MOS-PRICING-AND-TIMELINE.md)).
- **For the client's inputs:** every needed-by week comes earlier (decision register, questionnaire). The biggest change is infrastructure: from before W18 to **before W1**.
- **No reserve week.** The old plan kept W26 unallocated. Here the buffer is the pull-in list (standard depth is not promised) and the capacity lever at the W4/W8 checkpoints.
- **The go-live window is 6 weeks instead of 9,** with two weeks of hypercare instead of three. The data-migration and production-server work moves into the build window to make room.

## Alternatives considered

- **Keep 26 + 9 weeks** (ADR-0020): rejected by the owner's instruction.
- **Six months for procure-to-pay only, the rest later:** not what the instruction asks ("everything").
- **Squeeze the old 9-week go-live into the last 2–3 weeks:** rejected. UAT, training and migration need the client's people and calendar time; compressing them at the end is where projects fail. Rolling UAT and early servers spread that work across the build instead.
