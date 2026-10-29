# MOS on Odoo: the client's 28 screens against what is built

<!-- nav -->[MOS](../../README.md) › [3. Design](README.md)

- **Status:** 2026-10-29. A working list, kept current as screens are built. Written because the first UI pass put the fields on stock Odoo forms in a blue theme and called that done; it was not.
- **Source:** the client's P2P process v2, pages 2–25 ([images](../8-meetings/2026-10-03-client-documents/img/), [review §3.4](../7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md)). The mock-ups are illustrations of layout and fields; the review lists their defects (editable status dropdowns, mixed units, `PR-` prefix) and those are *not* copied.
- **Code:** `mos-odoo` (private). Screens are checked in a real browser, as the buyer, storekeeper or administrator that uses them, not only by unit tests.

## How the layout is applied

One mechanism, in `mos_theme` (`models/page_chrome.py`), gives **every MOS form** the client's page: a header (icon, title, one line on what the screen is for, where it sits), each titled part as a card, and the buttons in a bar at the foot, with the record history below instead of beside it. A form that needs a layout of its own (delivery entry, goods transfer, registration steps) writes it by hand. The sign-in page re-skins the one layout every sign-in page uses.

## Screen by screen

| Page | Client screen | State | What is still different |
|---|---|---|---|
| 2 | Sign-in | **Built** | The operation unit is chosen after sign-in (decision D2, FR-PLT-019), and that picker is **not built yet**: there is no "current site" per user |
| 3–4 | Supplier registration (5 steps) | **Built** | "Date registered" is not shown (the record's creation date is the date); status is shown as Candidate and is not editable (review defect) |
| 5 | Purchase request | Partly | Header, parts, line grid and totals follow the screen; priority is radio buttons, not coloured pills |
| 6 | Purchase order | Partly | Page header, "Part 1" and "Part 2" cards, line grid and totals follow the screen; it is still Odoo's order form underneath, with its own buttons in the header and Odoo's status bar instead of the Draft / Issued / Pending / Canceled pills; the company currency of the demo is USD, not birr |
| 7 | Supplier delivery entry | **Built** | — |
| 8 | QC inspection | Partly | Parameters, pass/fail pills, summary and "analysed by / checked by" follow the screen; "inspection type", "document no", "address of wheat", and the quantity in quintals and bags are not shown |
| 9 | Goods receiving | **Not done** | Still Odoo's receipt form; no receiving summary, no "ready for GRN" status panel |
| 10 | Bin card | **Built** | — |
| 11 | Stock card | **Built** | — |
| 12 | Goods transfer | **Built** | Between two locations of one site, posted as one stock move; between sites is the transfer order |
| 13–15 | Physical count, review, adjustment | Partly | All steps exist (blind count, recount, review, import, print); the layout has no "counting information" side card and no matched/variance strip |
| 16–20 | Item, warehouse, store, silo, bin masters | **Not done** (silo has the page layout) | Items, warehouses and bins are Odoo's own forms |
| 21 | Supplier invoice | Partly | One invoice covers one item (billed quantity and price per kg), the screen shows several lines with tax columns, an attachments block and an approval block |
| 22 | Supplier-side payment request | Not adopted | Decision N-24 / question C-39 |
| 23–24 | Payment request review | Partly | The voucher lists its invoices and approval steps; the layout is the generic one |
| 25 | Payment execution | **Not done** | A payment is five fields. The screen has payment-method tabs, deposit slip upload, related documents, the invoices being paid with the amount for each, and approval information |

## Demo data that was wrong, and what was done

- Silo stock had been put in without a stock movement, so every bin card and stock card showed a closing balance far below zero. The demo now posts opening stock as dated inventory adjustments, and `tools/seed_lifecycle.sh … 0_ledger` reconciles an existing database. The default warehouse is called *Main Warehouse*.
- Every new version of a specification renamed its parameters and grades to "… (copy)". Fixed in the model (with a test) and cleaned in existing data by a migration.
- Still true: the milling history consumes far more wheat than the deliveries bring in, so the oldest rows of a stock card are large opening adjustments. The ledger now reconciles; the volumes are not realistic yet.
