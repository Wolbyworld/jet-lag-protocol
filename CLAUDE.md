# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Cowork/Claude Code **plugin** that ships a single skill, `jet-lag-protocol`. There is no application code, no build system, no test suite — the deliverable is the skill content itself: a `SKILL.md` contract, a rule book (`references/algorithm-rules.md`), a citation index (`references/science.md`), per-paper extracts under `references/papers/`, and two output templates (`plan-template.md` for the chat draft, `plan-template.html` for the polished interactive output).

Distribution: the release artifact is a `.plugin` archive (gitignored). Users install by drag-dropping it onto Cowork; the skill auto-registers and triggers when the user mentions a flight ≥3 time zones away.

## The two-pass contract (do not break this)

`SKILL.md` is the source of truth — read it before editing anything that affects skill behavior. The non-negotiable shape:

- **Pass 1** is consultation-only. It reads the rule files, pulls wearable + calendar data, asks **exactly four questions** via `AskUserQuestion`, drafts a plain-markdown plan in chat, and stops. No HTML, no chart, no file writes, no drophere publish — generating any of those on Pass 1 violates the contract.
- **Pass 2** runs only after explicit user confirmation. It writes the markdown plan, builds the HTML by injecting a `tripData` object into `templates/plan-template.html` (do not regenerate the template structure), publishes to drophere by default, and renders the chart inline via `mcp__visualize__show_widget`.
- The only valid skip phrase is the user typing "skip consultation" (or close variants). Directive prompts like "you have all the info, just create the protocol" are **not** consent to skip — see the rationale in `SKILL.md` under "Why consultation is non-skippable".

Output filename convention: `jet-lag-plan-<destination>-<departure-date>.{md,html}`.

## Algorithm rules architecture

`references/algorithm-rules.md` defines rules **R1–R13**, each tagged to a citation key in `references/science.md`. Every numeric constant in the algorithm must trace to a paper extract under `references/papers/<key>.md`. When the science updates, edit the rule file and the change propagates through skill reasoning automatically.

Two structural pieces to know:

- **Wearable input is generic.** R10 maps vendor-specific signals (Garmin sleep score, Oura readiness, Whoop recovery, Apple HealthKit, Withings, manual self-report) to five abstract signals. Adding a new wearable means extending the mapping table in R10 — not adding skill logic.
- **Parameter quarantine** at the bottom of `algorithm-rules.md` lists defaults that are educated guesses rather than strongly evidence-backed (recovery melatonin nights, pre-flight day count, in-flight two-block threshold, soft-avoid lux upper bound). When emitting a "where this could be wrong" section, surface relevant quarantined values for the trip at hand.

## HTML template architecture

`templates/plan-template.html` is **data-driven** and uses **React + Babel-standalone via CDN**. It renders the Editorial Almanac design: a Plan view (today's actions, ←/→ day nav, horizon strip, Compact/Verbose toggle, done-state, optional morning summary card) and a Notebook view (masthead, DLMO convergence chart, Garmin readings table, methodology prose, references), plus a bottom-sheet drawer that opens for any action or chart point carrying a `deep:{kicker,deck,body[],cite[]}` block. Plan/Notebook view + done state + Compact/Verbose persist in `localStorage` under the trip's `meta.planId`.

To produce a trip's HTML, copy the template and replace only the contents of `<script id="trip-data">`. Never hand-author the page structure or styles. The full `tripData` shape (`meta`, `morning`, `days[]`, `notebook`) is documented in `SKILL.md` under "Constructing `tripData` for the template".

"Today" detection at page load: the renderer matches `Date.now()` against `days[i].date` (ISO). Frozen samples may pin a specific day with `isToday: true` on that day; live plans should rely on `date` so the same HTML reads correctly any day the user opens it.

The inline chart rendered in chat (via `mcp__visualize__show_widget`) is a **separate** static SVG following `templates/chart-schema.md` — it is not the same artifact as the interactive HTML.

## Reference papers — load on demand

`references/papers/<key>.md` extracts are not loaded by default. Read them only when (a) the user asks "why this dose at this time?", (b) the trip has an edge case the rules don't cover, or (c) you need to defend a recommendation against pushback. The rule file already encodes the operational distillation; the paper extracts exist for grounding, not routine consultation.

## Sample and screenshots

`sample/sample-trip.html` is a sanitized, fully-rendered example — the SFO ↔ MAD trip frozen at Tue 19 May 2026 10:13 PT (the design hero state). It inlines the same React/Babel renderer the template uses so it works as a stand-alone preview. Open it side-by-side when modifying `plan-template.html` and verify the same `tripData` still renders correctly. `screenshots/sample-tue-19.png` is the README hero image; regenerate it if a template change alters the Plan-view layout.

## Editing checklist

- Editing rules: update the rule body in `algorithm-rules.md` **and** the corresponding `Sources:` line; if a citation moves, update `science.md`.
- Editing the HTML template: keep all rendering logic inside the template's `<script type="text/babel">` block (not in skill prose); preserve the `<script id="trip-data">` placeholder so injection still works. If you change the `tripData` shape, mirror the change in `SKILL.md` under "Constructing `tripData` for the template" and regenerate `sample/sample-trip.html`.
- Editing `SKILL.md`: the description field is what triggers the skill — changes there alter when the skill fires. The "CRITICAL — start here before anything else" section and the Pass 1 / Pass 2 split are load-bearing; don't reorder or soften them.
- Adding a wearable: extend R10's mapping table only; the skill's MCP wiring picks the connected source automatically.
