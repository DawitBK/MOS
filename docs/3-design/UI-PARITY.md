# MOS on Odoo: the client's 28 screens against what is built

<!-- nav -->[MOS](../../README.md) › [3. Design](README.md)

- **Status:** 2026-10-29, end of day. Every screen of the client's list now has a page of its own in the running system, checked in a real browser as the person who uses it. The differences that remain are listed on this page, screen by screen, and each one is a decision or a missing detail, not a missing screen.
- **Source:** the client's P2P process v2, pages 2–25 ([images](../8-meetings/2026-10-03-client-documents/img/), [review §3.4](../7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md)). The mock-ups are illustrations of layout and fields; the review lists their defects (editable status dropdowns, quantities of different units added together, `PR-` prefix, a supplier reference shown twice) and those are *not* copied.
- **Code:** `mos-odoo` (private). A screen counts as built when it was opened as the role that uses it and the step it exists for was done in it (a supplier registered, a payment made, a bin created, a receipt turned into a GRN), not when the unit tests passed.

## How the layout is applied

One mechanism, in `mos_theme` (`models/page_chrome.py`), gives **every MOS form** the client's page: a header (icon, title, one line on what the screen is for, where it sits), each titled part as a card, and the buttons in a bar at the foot, with the record history below instead of beside it. A form that needs a layout of its own writes it by hand: delivery entry, goods transfer, supplier registration, payment, supplier invoice, goods receiving, payment request review, count, adjustment, the masters. The sign-in page re-skins the one layout every sign-in page uses.

## Screen by screen

| Page | Client screen | State | What is different, and why |
|---|---|---|---|
| 2 | Sign-in | **Built** | The operation unit is asked for after sign-in (decision D2, FR-PLT-019) for a person with several units, shown in the user menu and used as the default of every new record |
| 3–4 | Supplier registration (5 steps) | **Built** | "Date registered" is the creation date; the status is Candidate and is not an editable dropdown (review defect) |
| 5 | Purchase request | **Built** | The request status is not an editable dropdown (review defect); priority is Low / Medium / High / Urgent as coloured pills |
| 6 | Purchase order | Partly | Page header, "Part 1" and "Part 2" cards, line grid and totals follow the screen; it is still Odoo's order form underneath, with Odoo's status bar instead of the Draft / Issued / Pending / Canceled pills |
| 7 | Supplier delivery entry | **Built** | — |
| 8 | QC inspection | **Built** | Delivery note, address of the wheat, quantity in quintals and in bags added. "Document No" is not shown (it is unexplained in the document, review #34) and there is no "inspection type" (only incoming deliveries are inspected here) |
| 9 | Goods receiving | **Built** | The receipt with general information, ordered / accepted / rejected-or-short quantity per item, one total for each unit and the Ready-for-GRN panel; "accepted quantity with bags" is not a column (bags are counted at the gate) |
| 10 | Bin card | **Built** | — |
| 11 | Stock card | **Built** | — |
| 12 | Goods transfer | **Built** | Between two locations of one site, posted as one stock move; between sites is the transfer order |
| 13–15 | Physical count, review, adjustment | **Built** | Counting-information card, bin, status and remarks per line, matched / variance strip, and one total for each unit (the mock-up adds kilograms, bags and pieces together); the adjustment shows increase, decrease, before and after, net, type and who approved |
| 16 | Item master | **Built** | "Is raw material" is the material type *Raw material*; inspection required is kept in the item's QC policy, so there is one answer |
| 17 | Warehouse master | **Built** | Capacity is in kilograms only (pallets and cubic metres cannot be measured from the ledger); the receipt, issue and transfer switches are enforced when a stock move is done |
| 18 | Store master | **Built** | The stock valuation method is not kept per store (it is a property of the item category) |
| 19 | Silo master | **Built** | Type, description, minimum / maximum / safety levels that act (a load above the maximum is refused, a run cannot draw below the safety stock); capacity unit is kilograms only |
| 20 | Bin master | **Built** | — |
| 21 | Supplier invoice | **Built** | Several items, each matched on its own; attachments are the typed document list, not three upload buttons; no "discount" line and no "invoice type" (nothing behind them) |
| 22 | Supplier-side payment request | Not adopted | Decision N-24 / question C-39 |
| 23–24 | Payment request review | **Built** | Approve / send back / reject are on the document for the person whose turn it is; the submitter's comment is not a field (there is none in the process) |
| 25 | Payment execution | **Built** | One payment pays several invoices with an amount for each (the allocation table the review asked for); "Save as draft" is not adopted (a payment is final when made, with a gap-free number); "Other payment" is mobile money; the deposit slip is a list entry, not a thumbnail |

## Data that was wrong, and what was done

- Silo stock had been put in without a stock movement, so every bin card and stock card showed a closing balance far below zero. The demo now posts opening stock as dated inventory adjustments, and `tools/seed_lifecycle.sh … 0_ledger` reconciles an existing database. The default warehouse is called *Main Warehouse*.
- Every new version of a specification renamed its parameters and grades to "… (copy)". Fixed in the model (with a test) and cleaned in existing data by a migration.
- Choosing a supplier on an invoice did not fill its VAT, withholding and terms, so the form saved 0% tax. Fixed.
- The supplier ageing split a payment over the invoices of its voucher in proportion; it now counts what each payment allocated to each invoice.

## Still open

- **Demo money.** The demo company works in US dollars with prices of a few cents a kilogram. The client's screens show birr with prices in the tens. Changing the company currency after the orders exist would break matching (the order and the invoice must agree), so it needs a re-seed with birr prices, which is not done.
- **Demo volumes.** The milling history consumes far more wheat than the deliveries bring in, so the oldest rows of a stock card are large opening adjustments. The ledger reconciles; the volumes are not realistic.
- **Purchase order** (page 6) is Odoo's own order form underneath.
- **Ageing and payment reversals.** The supplier ageing view counts a reversed payment as paid, because the reversal lives in a later module. A reversed payment frees its invoices on the voucher and the payment screen, not in the ageing.
