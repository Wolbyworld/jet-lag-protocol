# Jet-lag protocol — Cowork plugin

A circadian phase-shift planner for crossing time zones. Pulls your wearable data, asks four targeted questions about the trip, and generates a personalized day-by-day plan plus an interactive HTML chart.

Built from primary sleep-medicine literature. Every numeric constant in the algorithm traces back to a specific paper — Khalsa 2003 (light PRC), Burgess 2010 (0.5 mg melatonin PRC), Zeitzer 2000 (light dose-response), Burke 2015 (caffeine), Eastman & Burgess 2009 (operational protocols), Borbély 1982 (two-process sleep model), and Burgess 2014 + Kantermann 2015 (DLMO from chronotype).

## What's inside

A single skill — `jet-lag-protocol` — that fires whenever you mention an upcoming flight ≥3 time zones away.

### Two-pass flow

**Pass 1 — Consultation (always asks first, never skipped):**

1. Reads the algorithm rules (`references/algorithm-rules.md`) and primary literature index (`references/science.md`).
2. Pulls last-7-days data from any connected wearable (Garmin, Apple Watch, Oura, Whoop, Withings) and applies sleep-quality modulation.
3. Asks four clarifying questions via `AskUserQuestion`:
   - Must-be-sharp moments at destination (presentations, board meetings, etc.)
   - Hard constraints during the trip (virtual meetings on home TZ, alcohol, summit overlaps)
   - Substances and health (caffeine OK / melatonin OK / meds / illness)
   - How many hours per day you're willing to shift pre-flight (0 / 0.5 / 1 / 1.5)
4. Drafts the plan as plain markdown in chat.
5. Waits for confirmation.

**Pass 2 — Polished output (only after confirmation):**

1. Saves a markdown plan file to your outputs folder.
2. Saves a polished HTML file built from `templates/plan-template.html` with your trip's data injected.
3. Publishes the HTML to drophere (or another sharing platform) and returns the URL.
4. Renders the chronotherapy chart inline via `show_widget`.

### The HTML output

V7 design — one focus card showing today's actions, a 12-day horizon strip below it, and three collapsible disclosures (Garmin context · high-leverage moments · methodology and sources). Tap any day in the horizon to inspect it. Built mobile-first, light theme.

## Algorithm rules

13 numbered rules in `references/algorithm-rules.md`, each tagged to citations. Highlights:

- **R1**: phase markers (DLMO, CBT_min) from chronotype + bedtime
- **R3**: adaptation rates — 1.5 h/day delay (westward), 1.0 h/day advance (eastward)
- **R4**: four-tier light intensity model (strong-seek / mild-seek / soft-avoid / full-avoid) — discretization of Zeitzer 2000 sigmoid
- **R5**: light windows pinned to CBT_min — peak-delay 2–5h before, peak-advance 2–5h after
- **R6**: 0.5 mg melatonin PRC — peak advance 2–4 h before DLMO
- **R7**: caffeine cutoffs — 8 h before bed for advances, 6 h for delays
- **R8.1**: sleep-bank rule — pre-flight bedtime overrides the protocol drift if logistics demand an early wake
- **R10**: wearable-driven intensity modulation
- **R11**: short-trip skip (<3h shift, <3 nights → stay on home time)
- **R13**: slow caffeine metabolizer extension

Plus an explicit **parameter quarantine** section at the bottom of `algorithm-rules.md` listing every numerical default that's based on N=1 or estimation rather than firm data.

## Design constraints

- **Calendar and wearable data don't replace consultation.** They show schedule and recovery state, not priority or constraints. The four questions extract a layer of information that does not live in any data source.
- **The skill never assumes melatonin or caffeine availability** — both require explicit user opt-in per trip.
- **Drophere publishing is default-on** (the user owns their drophere instance; published URLs are personal).

## Install

Drag-drop the `.plugin` file onto Cowork. The skill auto-registers. On the next session, mention an upcoming flight ≥3 time zones away and it'll trigger.

## License

MIT. Use it, fork it, improve it.

## Author

Alvaro Martinez Higes · `alvaro@luzia.com`
