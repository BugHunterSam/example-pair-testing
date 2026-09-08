# Canwi trust sweep — charter pack v1

How to use: paste the **report format block** plus **one charter** into a Claude in Chrome session (or a `claude --chrome` session) at a time. Review the findings, paste the rows into the bug catalogue, then run the next charter. One charter per session keeps the exploration tight and the context clean.

---

## Report format block (prefix every charter with this)

You are running a structured exploratory testing charter against Canwi, an Australian consumer financial planning tool. The single quality criterion is **user trust** — a user who doubts one number doubts every number. Stay inside the charter's scope; note out-of-scope observations in one line and move on. Use obviously fake test data, never real personal or payment details, and don't submit anything irreversible.

For each issue found, report one row:
| Description | Threat to trust (out of 3) | Category | Notes |

- Threat to trust: 3 = user would doubt the numbers or lose data (trust-critical); 2 = confidence-eroding polish/consistency problem (major); 1 = cosmetic (minor).
- Description: one line, starting with where it happens.
- Notes: repro in 1–3 lines, plus which heuristic surfaced it.

Finish with a markdown table of all rows sorted by trust score descending. "Nothing found under this charter" is a valid result — don't pad.

---

## Charter 1 — Projection consistency (the numbers must agree with themselves)

**Explore** the retirement projection flow end to end — inputs, timeline, chart, summary figures, and any tables —
**with** one fixed persona (e.g. age 45, $150k salary, $180k super, retire at 60) held constant while cross-checking every place the same number appears: chart hover values vs axis vs summary card vs table, today's-dollars vs nominal labelling, and the effect of toggling any assumption and toggling it back,
**to discover** whether Canwi ever disagrees with itself — the FEW HICCUPPS "Product" oracle — because an internal inconsistency of even a few dollars is the fastest way to make a user doubt the whole model.

## Charter 2 — Age Pension taper behaviour at the boundaries

**Explore** the Age Pension modelling on a retirement scenario,
**with** asset levels stepped just below, on, and just above the assets-test thresholds (full pension cut-in and cut-out points) for a single homeowner, then a couple, then a renter — plus an extreme ($10M assets, expecting $0 pension; near-zero assets, expecting full pension) —
**to discover** whether the taper behaves smoothly and plausibly at every boundary (no cliffs, no negative pension, no pension paid to millionaires), since this is Canwi's signature differentiator and any wobble here is maximally trust-damaging.

## Charter 3 — Hostile data in money and date fields

**Explore** every input on plan setup and event creation (salary, balances, contribution amounts, dates of birth, retirement dates, event dates),
**with** Elisabeth Hendrickson's data-type attacks: $0, negative amounts, $99,000,000, decimals in whole-dollar fields, thousands separators typed by hand (150,000), leading zeros, pasted values with a $ sign; a 29 Feb birthday, a retirement date before today, a retirement age of 54 and of 95, an event dated after death/plan-end,
**to discover** whether bad input is rejected with a clear message, silently mangled, or — worst — silently accepted and fed into the projection, producing confident-looking wrong numbers.

## Charter 4 — Save integrity and interruption (don't lose my plan)

**Explore** editing and saving a plan,
**with** interruption modes: refresh mid-edit, browser back mid-flow, double-clicking save, network throttled to slow 3G during a save (Chrome DevTools), the same plan open in two tabs with conflicting edits, and returning after a long idle to a possibly expired session,
**to discover** whether edits are ever partially saved, silently dropped, or overwritten without warning — because losing or corrupting someone's financial plan is the single most trust-destroying failure a planning tool can have.

## Charter 5 — Device, zoom, and accessibility on core screens

**Explore** the plan dashboard, projection chart, and event editor,
**with** an iPad-sized viewport (the iPad scroll-past-elements bug already in the catalogue suggests siblings), a narrow phone viewport, 200% browser zoom, keyboard-only navigation of one full flow, and a quick colour-contrast pass on the chart (is any meaning conveyed by colour alone?),
**to discover** where layout, scroll, focus, or charts break in ways that read as carelessness — each one a small withdrawal from the trust account, especially for users demoing the product on a tablet.

---

Suggested order: 2 → 1 → 3 → 4 → 5. Charter 2 first because the Age Pension taper is the differentiator; if it's solid, that's also a marketing-usable fact.
