# ADR-0020: One release with all six modules at core depth in 26 weeks, on every team path

- **Status:** Accepted, 2026-09-28 (owner decision in the planning Phase 6 session). Amends [ADR-0018](ADR-0018-lean-v1-six-months.md): it fixes both the date and the scope, where ADR-0018 let the scope move per path.
- **Trigger:**
  - Planning Phase 4 estimated v1 bottom-up ([implementation plan](../MOS-IMPLEMENTATION-PLAN.md) §3–§5).
  - It found that no path builds all six modules in 26 weeks at the likely estimate, and offered fixed-date, two-release and longer options.
  - The owner rejected the longer options: "one release, all six cores … cap it at 6 months, I can do it". The owner also asked for every team path to be laid out inside that window.

## Context

- **The estimate.**
  - Lean-complete v1 is 3,102 AI-assisted build hours (likely), over 187 features that cover all 205 v1 requirements.
  - Core depth of every module is 2,380 hours, including hardening.
- **Capacity in 26 weeks at the likely rate,** after an 80% focus factor and a reserve week:

  | Path | Build hours available |
  |---|---|
  | A: 2 developers + QA | 2,080 |
  | B: 2 developers, who also test | 1,809 |
  | C: 1 developer | 974 |

- **Six modules at core at the likely rate** need 34 weeks (A), 39 (B) or 63 (C).

## Decision

1. **The release is all six modules at core depth.**
   - It is built in W1–W26 and goes live in W27–W35.
   - This holds on every team path. The client chooses the path from the [commercial proposal](../MOS-COMMERCIAL-PROPOSAL.md).
2. **Each path is planned at the velocity it needs.** The estimate stays the reference that progress is measured against.

   | Path | Velocity (× the likely estimate) | Each estimated hour done in |
   |---|---|---|
   | A | **1.32** | 46 minutes |
   | B | **1.52** | 40 minutes |
   | C | **2.50** | 24 minutes |
   | Reference A+ (3 developers + QA) | 1.01 | 59 minutes |

3. **Standard and full depth are the pull-in list** (606 + 116 estimated hours):
   - they are built in the release only if measured velocity runs ahead;
   - otherwise they are v1.1;
   - procure-to-pay standard items come first.
4. **Checkpoints at W4 and W9.**
   - Actual hours per finished feature are divided by the estimate, and the result is compared with the path's required factor.
   - **Behind by more than 10%:** capacity is added before W10, either more hours from the second developer or a third developer. The date and the core scope do not move.
   - **Ahead:** standard items are pulled in.
5. **The FRD month tags follow the path A schedule** (FRD 0.3.3). Requirements delivered only at standard or full depth are tagged "v1 (pull-in; else v1.1)".

## Consequences

- **For the client:** all six modules live about eight months after the go-ahead, at core depth.
- **Execution risk:**
  - The plan depends on AI-assisted velocity above the likely estimate. Path A sits at the optimistic end of the estimate range; paths B and C are beyond it.
  - This is recorded as risk R22 and made visible by the W4 checkpoint, early enough to add capacity.
- **Path C:** one developer at 2.5× the likely rate, with no cover for illness. Its go-live needs about 16 person-weeks inside the 9-week window, so path C needs a second person in the go-live window, or the client's key users carrying more of UAT and training.
- **Who pays for capacity added after a checkpoint** is an open owner decision: register O-26.

## Alternatives considered

The implementation plan v0.4 drafts before this decision carried these options:
- **Fixed date, scope per path:** path A would deliver procure-to-pay only.
- **Two releases:** procure-to-pay live at W35, the rest about W46.
- **One release at the likely velocity:** live at W43 on path A.
- **Lean-complete v1:** W1–W41 on path A.

The owner rejected all four.
