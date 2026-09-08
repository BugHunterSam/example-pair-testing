# Risk heuristics reference

The baked-in spine the risk-identification step applies. These are established
testing heuristics from trusted sources, restated as **lenses** — questions you
ask of a behaviour to surface risks. Apply every relevant lens to each acceptance
criterion. A risk is a *specific way the behaviour could fail or disappoint*, not
a restatement of the requirement.

Attribute risks to the lens that surfaced them (e.g. "Data-type attack — strings").
When an AC touches a specialised domain the orchestrator may web-search for
domain-specific risks to top this set up; this file is the floor, not the ceiling.

---

## Lens 1 — Product coverage: SFDIPOT (HTSM, James Bach / Michael Bolton)

Walk the behaviour through each element. What could go wrong in each?

- **Structure** — what the product is made of: files, code paths, modules. Risk: a code path the AC implies is never reached.
- **Function** — what it does: primary function, error handling, calculations, transformations. Risk: the stated function works but an adjacent function it touches breaks.
- **Data** — what it processes: input, output, defaults, preset, persistent, sequences, big/small/none, invalid. (See Lens 4.)
- **Interfaces** — how it connects: APIs, UI, imports/exports, other systems. Risk: contract mismatch, partial integration.
- **Platform** — what it depends on: OS, browser, device, screen size, locale, third-party services. Risk: works on one platform, fails on another.
- **Operations** — how it's used: real-world scenarios, frequent vs rare flows, abuse. Risk: a realistic usage pattern the AC didn't consider.
- **Time** — when things happen: timeouts, concurrency, sequence/order, day-boundaries, DST, race conditions, slow/fast. Risk: correct in isolation, wrong under timing.

## Lens 2 — Quality criteria: CRUSSPIC STMP (HTSM)

For each, ask "could this behaviour violate this quality?":
Capability, Reliability, Usability, Security, Scalability, Performance,
Installability, Compatibility — Supportability, Testability, Maintainability,
Portability. Localizability/Internationalization.

The ones most often missed in ACs: **Security, Performance, Usability,
Accessibility, Compatibility, Localizability.**

## Lens 3 — Oracles: FEW HICCUPPS (how would you *know* it's wrong?)

A risk often hides in a missing oracle — a thing the behaviour is silently
inconsistent with:
- **F**amiliar — inconsistent with comparable problems/known bug patterns.
- **E**xplainable — behaviour you can't explain to a user.
- **W**orld — inconsistent with how the real world works.
- **H**istory — inconsistent with the product's past behaviour (regression).
- **I**mage — damages the brand/reputation.
- **C**omparable products — a competitor does it differently/better.
- **C**laims — inconsistent with what marketing/docs/the AC itself promises.
- **U**ser expectations — surprises a reasonable user.
- **P**roduct — internally inconsistent (one screen contradicts another).
- **P**urpose — fails the actual goal even if it meets the letter of the AC.
- **S**tatutes — violates a law/regulation (GDPR, accessibility law, finance rules).
- **S**tandards — violates an industry/internal standard.

## Lens 4 — Data-type attacks (Elisabeth Hendrickson, *Test Heuristics Cheat Sheet*)

For every input or stored field, consider hostile/edge data:

- **Strings:** empty, very long (overflow field/DB column), leading/trailing
  whitespace, unicode, emoji, RTL text, accented chars, SQL meta-chars (`'`, `--`),
  HTML/script (`<script>`), template/format strings (`%s`, `{{x}}`), newlines,
  null bytes, names like `Null`, `True`, `O'Brien`.
- **Numbers:** zero, negative, very large (int overflow / `MAX_INT`), very small,
  decimals where integers expected, leading zeros, scientific notation,
  thousands separators, currency precision/rounding, division by zero.
- **Dates & time:** boundaries (midnight, month/year end), leap years (29 Feb),
  DST transitions, timezones (UTC vs local), epoch/`0`, far-future/far-past,
  ambiguous formats (DD/MM vs MM/DD), invalid dates, start-after-end ranges.
- **Files:** empty file, huge file, wrong type, double extension, no extension,
  corrupt, zero-byte, very long filename, special chars in name.
- **Absence:** null, missing field, default not applied, optional treated as required.

## Lens 5 — Boundaries & partitions (classic: BVA + equivalence partitioning)

- For any range or limit, test **min-1, min, min+1, max-1, max, max+1**.
- **Goldilocks:** too big, too small, just right; too many, too few, none, one.
- One valid value per equivalence class; one invalid value per class.
- Off-by-one, fencepost, inclusive vs exclusive bounds.

## Lens 6 — CRUD lifecycle

For any entity the behaviour touches: **C**reate, **R**ead, **U**pdate,
**D**elete — plus the awkward combinations: create-then-delete-then-read,
update a deleted item, read before create, duplicate create, delete twice,
concurrent update of the same record, ordering/sorting after edit.

## Lens 7 — Failure & interruption modes

Ask "what if it doesn't complete cleanly?":
- Network drop / timeout / slow connection mid-operation.
- Server 500 / 4xx / unexpected response shape.
- Partial save — some data written, some not (atomicity).
- Double-submit / double-click / rapid repeat.
- Concurrency — two users/tabs acting on the same thing (race, lost update).
- Session expiry / token refresh mid-flow.
- Back button, refresh, navigate-away mid-flow.
- Retry / idempotency — does retrying duplicate the effect?
- Permissions — actor lacks rights; rights revoked mid-session.
- Resource limits — quota/rate-limit/disk/memory hit.

## Lens 8 — State & flow

- Entry from an unexpected prior state.
- Interrupted/resumed flow; abandoned half-finished.
- Order-dependence — does step B work if step A was skipped?
- Empty states, first-run, and the "very full" state.
- Idempotent re-entry — doing the same action twice.

## Lens 9 — Human & accessibility

- Keyboard-only and screen-reader operation; focus order; visible focus.
- Colour-contrast / colour-only meaning; text resize/zoom.
- Localisation: long translated strings, RTL layout, locale number/date formats.
- Cognitive load / error recovery — can a confused user undo?

---

## How to use these lenses

1. Read the AC and its context.
2. Walk Lenses 1–9 in order. For each, write down only the *specific, plausible*
   risks for *this* behaviour — skip lenses that genuinely don't apply, and say so.
3. Phrase each risk as a failure: "If/when X, then Y goes wrong, harming Z."
4. Tag each with its lens. De-duplicate near-identical risks.
5. Don't pad. A focused list of real risks beats an exhaustive list of generic ones.

## Sources

- Elisabeth Hendrickson — *Test Heuristics Cheat Sheet* & *Explore It!* (charter format).
- James Bach & Michael Bolton — *Heuristic Test Strategy Model* (SFDIPOT, CRUSSPIC STMP, FEW HICCUPPS).
- Ministry of Testing — community test-heuristics and risk-storming material.
- Classic test design — Boundary Value Analysis, Equivalence Partitioning.
