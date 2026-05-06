---
name: jet-lag-protocol
description: Two-pass circadian jet-lag planner. Pass 1 ALWAYS asks 4 clarifying questions (must-be-sharp moments at destination, hard constraints during the trip, health/meds, aggressiveness preference) and pulls wearable data, then drafts a plain-text plan and waits for user confirmation. Calendar and wearable data do NOT replace the consultation — they show schedule and recovery state, not priority or constraints. Pass 1 fires even on directive prompts like "you have all the info, just create the protocol"; only the phrase "skip consultation" disables it. Pass 2 runs after user confirms, producing both the markdown plan and the polished HTML file by default (drophere/shareable URL is opt-in). Use for any upcoming flight across time zones — sleep/light/melatonin/caffeine plan, jet-lag avoidance, recovery, body-clock shifting, chronotherapy. Triggers: "jet lag plan", "shift my clock", "flying to X next week", "prepare for travel", "circadian protocol", "phase shift", shift work, DSPS/ASPS.
---

# Jet-lag protocol — circadian phase-shift skill

Generates a personalized day-by-day plan covering pre-flight, in-flight, post-arrival adaptation, and recovery, plus a graphical chronotherapy chart. Built from primary circadian-biology literature; every rule traces to a citation.

## CRITICAL — start here before anything else

This skill MUST run as a **two-pass flow**. Do not generate any plan, chart, or HTML on Pass 1. The Pass 1 deliverable is the four consultation questions, asked via `AskUserQuestion`, **and nothing else of substance**. Computing the plan, rendering the chart, or saving any output file on Pass 1 violates the skill contract.

### Why consultation is non-skippable, even with rich data

Calendar gives you what's scheduled. Wearable gives you what's measured. **Neither tells you what's important.** A user with 12 calendar events at the destination doesn't have 12 must-be-sharp moments — they probably have 1–3, and the rest are routine. Without asking, you cannot distinguish a board presentation from a coffee chat. The algorithm uses must-be-sharp moments to anchor strong-seek light blocks, suppress melatonin in the preceding 4 h, and verify the adaptation timeline lands in time. If you skip the question, you're optimizing the protocol against the wrong constraint set.

This applies **even when the user writes a directive prompt** like:
- "You have all the info, just create the protocol"
- "Use my calendar and Garmin"
- "I trust your defaults"
- "Just give me the plan"

These prompts are not consent to skip consultation. They are consent to **not waste their time on basic input gathering** — which the skill avoids anyway by reading calendar/wearable directly. The four questions extract a layer of information that does not live in any data source: priority, constraints the user hasn't formalized, health state, risk tolerance.

The only valid skip phrase is the user explicitly typing **"skip consultation"** (or close variants like "no questions", "skip the quiz"). If they didn't type that, ask the questions.

### Pass 1 — minimal action checklist (run in order, every invocation)

1. Read `references/algorithm-rules.md` and `references/science.md` to load the rule set.
2. Confirm/gather basic inputs (origin TZ, destination TZ, depart and arrive datetimes, habitual bedtime, chronotype, caffeine/melatonin opt-in). Pull from calendar/profile where possible. Ask only for what's missing.
3. Pull wearable data from any connected source (Garmin, Apple Watch / HealthKit, Oura, Whoop, Withings, etc.) — last 7 days of sleep score, sleep duration, HRV trend, RHR trend, recovery proxy. Use the corresponding MCP. Apply rule R10 (sleep-quality modulation) silently — note the modulation in your scratch but do not present it yet.
4. **ASK THE FOUR CONSULTATION QUESTIONS** via a single `AskUserQuestion` batch (multiSelect false on each, free-text "Other" allowed). If you have calendar data, **show the inferred events in the question text** so the user can confirm/correct rather than re-list them:

   | # | Question (sample text — adapt to user's actual data) | Why |
   |---|---|---|
   | 1 | "I see these calendar events at destination: [list 3–5 most prominent from calendar]. Of these (or others not on the calendar), which do you need to be at *peak* alertness for? Presentations, board moments, key first meetings — pick the ones where being foggy would actually cost you." | Algorithm places strong-seek before, no-melatonin in 4 h prior, sanity-checks adaptation timeline against these. |
   | 2 | "Hard constraints during the trip — recurring virtual meetings on home TZ that you can't move (I see: [list any from calendar]), exercise commitments, dinners with alcohol, family obligations, summit/event overlaps?" | Algorithm reshapes melatonin / sleep / caffeine windows around these. |
   | 3 | "Substances and health for this trip — caffeine OK? Melatonin OK (default 0.5 mg)? Any other meds affecting sleep (sedatives, beta-blockers, antihistamines, hormonal contraceptives), planned alcohol, recent illness, or recovery debt I should know about?" Present 4 options: (a) Both caffeine + 0.5mg melatonin, no other concerns (default); (b) Caffeine only, no melatonin; (c) Melatonin only, no caffeine; (d) Other (free text — let user specify dose, meds, alcohol patterns, etc.). | Determines availability of R6 (melatonin) and R7 (caffeine) chemistry components. Affects caffeine cutoffs (R13), melatonin dose, intensity softening. **Without explicit opt-in, the algorithm defaults are unsafe to assume.** |
   | 4 | "How many hours per day are you willing to shift your sleep schedule **before** the flight? Bigger daily shifts = faster adaptation but harder to fit into normal life." Present 4 options: (a) **No pre-shift** — I'll arrive cold and adapt at destination only; (b) **0.5 h/day** — barely noticeable, just nudge bedtime/wake by ~30 min; (c) **1 h/day** — standard protocol intensity, default for most travelers; (d) **1.5 h/day** — literature maximum, I'll commit. | Sets the protocol's daily shift rate (R3) and therefore the number of pre-flight days needed and per-day bedtime/wake drift in R8.1. The sleep-bank rule overrides this on the night before an early flight regardless of choice. |

5. After the user answers, **draft the plan as plain markdown in chat** — phase markers, day-by-day schedule, leverage moments. No chart. No HTML. No URL. End the draft with one sentence: *"Confirm and I'll generate the chart and the polished HTML, or tell me what to adjust."*
6. **STOP.** Wait for explicit user confirmation ("looks good", "yes", "go ahead", etc.) before proceeding to Pass 2.

### Pass 2 — full output (only after Pass 1 confirmed)

**Non-negotiables for Pass 2** (every run, no exceptions unless user says "skip"):
- **Interactive HTML output.** The HTML file MUST include hover/tap tooltips on every Gantt block (light tiers, sleep, melatonin, caffeine, numbered leverage markers) AND bed/wake time labels inside the sleep blocks. Both behaviors are baked into `templates/plan-template.html`. Use the template — do not hand-build the HTML.
- **Drophere publish.** Publish the HTML file to drophere via the `drophere:publish` skill and return the URL inline. Drophere is the user's own service; publishing is default behavior, not opt-in. Skip only if the user explicitly typed "no drophere" / "local only" / "don't publish".
- **Inline chart in chat.** Render the chronotherapy chart inline via `mcp__visualize__show_widget` as well, so the user sees it without opening the HTML. The inline widget is static SVG (no tooltips); the HTML file is the interactive version.

Pass 2 always produces, in order:

1. **Polished markdown plan file** saved to outputs directory: `jet-lag-plan-<destination>-<departure-date>.md`.
2. **Polished HTML file** saved to outputs directory: `jet-lag-plan-<destination>-<departure-date>.html`. Built by **copying `templates/plan-template.html` and replacing the `<script id="trip-data">` block** with this trip's `tripData` object. Don't regenerate the HTML structure from scratch — the template handles the layout and only the data block changes. The template's renderer JS builds the SVG chart, leverage cards, Garmin section, etc. from `tripData` at page load.
3. **Drophere publish** of the HTML file via the drophere skill. The user owns the drophere service; published URLs are personal, not third-party shared. Default behavior — publish unless the user explicitly says "don't publish" / "local only" / similar. Return the URL inline.
4. **Inline chart render** in chat via `mcp__visualize__show_widget` so the user sees the chart without needing to open the HTML.

### Constructing `tripData` for the template

The template (V7 design — focus card + horizon strip + collapsible context) expects this shape:

```js
{
  trip: {
    title: "SF jet-lag plan",                      // bold heading
    sub: "Madrid → SFO → Madrid · 12–22 May 2026"  // small grey line
  },
  days: [
    {
      date: "2026-05-10",        // ISO YYYY-MM-DD — REQUIRED, drives "today" detection
      num: 10,                    // day-of-month for horizon
      dayShort: "Sun",            // short day label, can include "✈" for travel days
      phaseLabel: "D−2 · MAD",    // small caption under day name
      title: "Sun, 10 May",       // big title in focus card
      phaseFull: "Pre-flight · Day 1 — small drift later",  // sub-line in focus card
      isKey: false,               // true for high-leverage days (gold cream tint)
      dots: ["s","l",""],         // 3 dots in horizon: "s"=sleep, "l"=light, "m"=melatonin, ""=none
      actions: [
        // Each action shows as a row: icon + time + headline + (optional why)
        // type ∈ {wake, bed, lights, lightsoft, mel, caf, fly, note}
        { type: "wake", time: "06:30", h: "Wake — sunglasses outside before 09:00",
          why: "Stops morning light advancing your clock against delay direction.",
          star: false },
        { type: "lights", time: "19:00", h: "Outdoor walk after dinner" },
        { type: "bed",  time: "23:00", h: "Bed", star: true }
      ],
      why: "Optional longer rationale shown in expandable 'Why this day matters' below the actions."
    },
    // ...one entry per day
  ],
  todaySummary: "<strong>Today's job:</strong> ... <br><br><strong>Hardest day coming:</strong> ...",
  // Shown only when current date == one of the trip days.

  garmin: {  // optional — entire Garmin disclosure hides if null/missing
    stats: [
      { num: "75", label: "Sleep score 7d", read: "FAIR", kind: "ok" },
      // kind: "ok" (green left-bar) | "flag" (orange left-bar)
    ],
    adjustments: [
      { head: "Mon 11 May bedtime moved 21:00 → 22:00",
        body: "Real sleep target is ~6h30, not 7h+. Body may contain HTML." }
    ]
  },

  leverage: [  // numbered ★ moments — shown in the 'High-leverage moments' disclosure
    { num: 1, when: "Mon 11 · 22:00 CEST", what: "Sleep-bank night",
      why: "Bedtime calibrated to 6h30 sleep target before early flight." }
  ],

  methodology: {  // optional
    paragraphs: [
      "Plain text or HTML. Use <strong> for emphasis.",
      "Multiple paragraphs allowed."
    ],
    sources: "Khalsa et al. 2003 · Burgess et al. 2010 · ..."
  }
}
```

**Action type → icon map** (handled by template):
- `bed` 🌙 dark navy · `wake` ↑ cream · `lights` ☀ amber (strong-seek)
- `lightsoft` ◐ grey (soft-avoid) · `mel` ● purple (melatonin)
- `caf` ☕ brown · `fly` ✈ sky-blue · `note` ! red (constraint/event)

**The template handles automatically:**
- Computing today's date and selecting the closest day as the default focus
- "Trip starts in N days" / "Today · D±N" / "Trip ended N days ago" pill state
- Tap-any-day in the horizon strip to switch focus, with a Back button
- Smooth fade transition between days
- Showing `todaySummary` only when actually within the trip
- Building the three collapsible disclosures (Garmin / Leverage / Methodology) from data

Don't try to build the page HTML by hand — just write `tripData` and let the template render it.

## When to invoke

- User mentions an upcoming flight ≥3 time zones away
- User asks how to avoid or reduce jet lag
- User asks for a "circadian protocol", "phase shift schedule", "chronotherapy plan", or similar
- User asks for shift-work or DSPS/ASPS adjustment using the same mechanism

Skip if: shift is <3 h AND trip <3 nights (rule R11 — adaptation cost > jet-lag cost).

## Inputs needed

Gather these before applying the rules. Ask in one batch via `AskUserQuestion` if any are missing:

| Input | Required | Default if missing |
|---|---|---|
| Origin time zone | Yes | Infer from calendar / user location |
| Destination time zone | Yes | Ask |
| Departure date+time (origin local) | Yes | Pull from calendar if available |
| Arrival date+time (destination local) | Yes | Pull from calendar if available |
| Return flight (if round-trip) | Optional | Ask if applicable |
| Habitual bedtime (origin local) | Yes | 22:30 default |
| Habitual wake time (origin local) | Yes | Compute from bedtime + 7.5h |
| Chronotype | Yes | Intermediate default; ask "early bird / intermediate / late bird?" |
| Caffeine OK? | Yes | Yes default |
| Melatonin OK? | Yes | Ask explicitly |
| Melatonin dose available | If yes | 0.5 mg default; if user has 3+ mg, switch to Burgess 2008 PRC |

### Optional: wearable sleep-quality data

If the user has any of: Garmin Connect, Apple Watch (HealthKit), Oura, Whoop, Withings — query the corresponding MCP for the last 7 days and extract:

- 7-day mean sleep duration
- 7-day sleep score / continuity proxy
- HRV trend vs personal baseline
- RHR trend vs personal baseline
- Recovery proxy (body battery / readiness / recovery %)

Apply rule R10 (sleep-quality modulation) using these signals. If no wearable connected, ask: "How was your sleep last week — good / mixed / poor?" and apply the fallback in R10.

## Invocation flow

The flow is **two-pass**: a quick consultation that surfaces constraints, then the full plan once the user has confirmed. Do not skip the consultation. Do not produce a polished HTML / chart / drophere publish on the first pass.

### Pass 1 — Consultation

1. **Read `references/algorithm-rules.md`** — the full rule set. Always read on every invocation.
2. **Read `references/science.md`** — the citation index. Always read.
3. **Gather basic inputs** (TZ, dates, bedtime, chronotype, caffeine/melatonin opt-in) — see Inputs section above. Pull from calendar/wearable/profile where possible; ask for what's missing in a single batch.
4. **Pull wearable data if available** (last 7 days from Garmin / Apple Watch / Oura / Whoop / Withings or whichever MCP is connected) and apply rule R10 (sleep-quality modulation).
5. **Run the consultation pass.** Use `AskUserQuestion` (single batch, 4 questions) to surface trip-specific constraints the algorithm cannot infer:

   - **Must-be-sharp moments at destination.** "Are there destination events where you absolutely need to be at peak alertness — presentations, key meetings, social events with prep prerequisites?" Capture date + local time. The algorithm uses these to (a) sanity-check that the protocol has the user adapted by then, (b) place a strong-seek block right before the moment, (c) avoid prescribing melatonin in the preceding 4 h.

   - **Hard constraints during the trip.** "Anything that conflicts with normal protocol windows — recurring virtual meetings on home timezone, exercise commitments, dinner plans with alcohol, family obligations, summit overlap, etc.?" Capture as time-windowed constraints. The algorithm uses these to (a) move melatonin doses earlier or later, (b) drop pre-flight-shift aggressiveness on days with virtual meetings during target sleep window, (c) flag alcohol windows as "no-melatonin" + "expect worse sleep tonight."

   - **Health and recovery state.** "Any recent illness, medication that affects sleep (sedatives, beta-blockers, antihistamines, hormonal contraceptives — these change caffeine half-life and melatonin response), planned alcohol use during the trip, or recent physical trauma?" The algorithm uses this to (a) extend caffeine cutoffs for slow metabolizers per R13, (b) reduce melatonin doses if the user is on a sedative, (c) soften pre-flight intensity if recovering from illness.

   - **Aggressiveness preference.** "How aggressive do you want this protocol — full intensity (best result, requires discipline), pragmatic (skip the hardest parts if they conflict with life), or minimalist (just the highest-leverage moments)?" The algorithm uses this to (a) drop or keep optional pre-flight days, (b) reduce or keep the morning-before-flight wake-light prescription, (c) skip or keep recovery-protocol days post-trip.

6. **After consultation, draft a plain-text plan**: phase markers, day-by-day schedule, leverage-moment summary. Output as markdown in chat. Do NOT generate HTML/chart yet.

7. **Confirm with user** that the plan looks right before generating polished output. One terse sentence: "Confirm and I'll generate the chart and the shareable URL, or tell me what to adjust."

### Pass 2 — Polished output (only after Pass 1 confirmed)

8. **Apply rules R1 → R11 in order** (re-applied with consultation inputs folded in):
   - R1: estimate phase markers (DLMO, CBT_min) from chronotype + bedtime
   - R2: compute direction and magnitude of required shift
   - R3: compute adaptation rate and total days
   - R4 + R5: build per-day light windows (4-tier intensity)
   - R6: build per-day melatonin doses
   - R7: build per-day caffeine windows
   - R8.1: compute pre-flight bedtimes (apply sleep-bank rule)
   - R8.2: build in-flight sleep blocks
   - R8.3: build post-arrival sleep schedule
   - R9: add wind-down markers
   - R10: modulate intensity based on wearable signals (if available)
   - R11: short-trip skip-check
9. **Read paper extracts on demand** from `references/papers/<key>.md` *only* if:
   - User asks "why this dose at this time?" or similar grounding question
   - The trip has an edge case the rules don't cover (e.g. a 13 h shift that triggers the long-way-around rule, or a user reporting a non-default melatonin dose)
   - You need to defend or refine a recommendation against user pushback
10. **Emit the polished plan** following `templates/plan-template.md` exactly.
11. **Render the chronotherapy chart inline** by calling `mcp__visualize__show_widget` with an SVG following `templates/chart-schema.md`. Include interactive tooltips and bed/wake time markers.
12. **Save the markdown plan** to the outputs directory: `jet-lag-plan-<destination>-<departure-date>.md`. Required.
13. **Save the polished HTML** to the outputs directory: `jet-lag-plan-<destination>-<departure-date>.html`. Required by default — only skip if the user said "no HTML" / "markdown only". The HTML embeds the SVG chart with tooltips and Garmin personalization section.
14. **Optionally publish** the HTML to drophere or another sharing platform — opt-in only. Skip unless the user asked for a shareable URL ("publish", "share", "drophere it", etc.).

## Output contract

The plan markdown must contain:

- A header with trip parameters (origin, destination, dates, direction, magnitude)
- Derived phase markers section (DLMO, CBT_min, in both home and destination time)
- Strategy summary paragraph (direction, expected adaptation duration, key levers)
- Per-day schedule with light tiers, melatonin doses, caffeine windows, sleep blocks, wind-down markers
- Outbound and (if round-trip) return cheat-sheets
- "Where this could be wrong" section listing the protocol's biggest assumptions for this trip
- Citation footer referencing `science.md`

The chart must be inline (rendered via `show_widget`), not a saved file.

## Edge cases

- **Round-trip**: build outbound plan first, then return plan starting from the destination-adapted phase, then recovery in origin TZ.
- **Multi-leg flights with layovers >12 h**: treat as separate trips chained.
- **Shift >12 h**: apply the long-way-around rule from R2 — the actual phase shift is 24 − magnitude in the opposite direction.
- **Nighttime arrival (lands during destination biological night for body)**: adaptation begins one day late; day-0 protocol compressed.
- **Older user (>55)**: scale rates in R3 down by ~25% (PRC amplitude shrinks with age).
- **Slow caffeine metabolizer**: extend cutoff per R13.
- **No melatonin**: produce light-and-sleep-only plan; expected adaptation is ~25–40% slower; note in "where this could be wrong" section.
- **Short trip (<3 nights AND <3 h shift)**: skip protocol per R11; tell user to stay on home time.

## Tone of the output

- Direct, concise, prose-heavy with discrete schedule blocks where structure helps
- Cite mechanisms briefly when they justify a counterintuitive recommendation
- Always include the "where this could be wrong" honesty section
- Speak to a competent adult, not a patient — the user can handle "your CBT_min sits around X" without a glossary

## Failure modes to avoid

- **Do not skip the consultation pass.** Generating a plan from defaults without surfacing the user's must-be-sharp moments and life constraints produces protocols that conflict with the user's actual schedule, which leads to non-adherence. The consultation is the single most important step.
- **Do not generate the polished HTML or publish to a shareable URL on the first pass.** The plan should be drafted as plain markdown, confirmed, then polished. Polishing before confirmation wastes effort and locks in errors.
- Do not over-prescribe melatonin for westward shifts — the literature is tepid and the soporific effect doesn't justify aggressive phase-shifting use (R6).
- Do not output a 2 h single-day bedtime jump — cap at 1.5 h (per R3 max delay rate); 1 h is more realistic.
- Do not collapse light guidance to binary seek/avoid — use the four tiers from R4.
- Do not treat the no-caffeine block as derived; emit it as a positive block (R7).
- Do not output specific numerical parameters that aren't traceable to a paper extract — if asked for a constant the algorithm doesn't have, flag it as outside the protocol.
- Do not assume a 7 h+ sleep target without checking the wearable data. Many users sleep 6–6.5 h reliably; the sleep-bank rule (R8.1) is highly sensitive to the assumed sleep duration.
