# Plan template — output shape for the jet-lag-protocol skill

This is the canonical structure of every plan the skill produces. Follow exactly.

---

# Jet-lag plan — {{user_name_or_blank}}, {{trip_label}}

Built from the circadian-shift protocol (`jet-lag-protocol` skill v1.0). Algorithm rules applied: R1–R11. Wearable modulation: {{R10_status}}. Citations at the bottom.

## Inputs

| Field | Value |
|---|---|
| Home TZ | {{home_tz}} ({{home_tz_offset}}) |
| Destination TZ | {{dest_tz}} ({{dest_tz_offset}}) |
| Time-zone delta | **{{magnitude}} h {{direction_word}} ({{direction}})** |
| Outbound | {{outbound_summary}} |
| Return | {{return_summary_or_one_way}} |
| Habitual bedtime | {{habitual_bedtime}} home |
| Habitual wake | {{habitual_wake}} home |
| Chronotype | {{chronotype}} |
| Caffeine | {{caffeine_status}} |
| Melatonin | {{melatonin_status}} (dose: {{melatonin_dose}}) |
| Wearable input | {{wearable_signals_summary_or_none}} |

## Derived phase markers (you, at home)

- **DLMO** ≈ {{DLMO_home}} ({{DLMO_offset_explanation}})
- **CBT_min** ≈ {{CBT_min_home}} ({{CBT_min_offset_explanation}})
- **Wake-time entrainment cue** anchored at {{habitual_wake}}

In destination time:

- DLMO: {{DLMO_dest}}
- CBT_min: {{CBT_min_dest}}
- "Body says wake": {{body_wake_dest}}

## Strategy summary

A short paragraph (3–6 sentences) explaining:
- Direction (advance vs delay) and why
- Expected total adaptation days
- Where the trip's leverage points are
- Asymmetry note if round-trip (return is harder than outbound, or vice versa)
- Mention any wearable-driven modulation (e.g. "your 7-day HRV is below baseline, so the pre-flight drift has been softened to 0.5 h/day")

## Phase 1 — Pre-flight (origin)

For each pre-flight day (typically D−2 and D−1):

### {{day_name}} {{date}} (D−{{n}})

- **Bed**: {{bedtime}} ({{rationale_brief}})
- **Wake**: {{wake_time}}
- **Light tiers**:
  - Strong-seek {{ss_window}} — {{practical_action}}
  - Mild-seek {{ms_window}}
  - Soft-avoid {{sa_window}} — {{practical_action}}
  - Full-avoid {{fa_window}}
- **Caffeine OK**: {{ok_window}}. **No-caffeine block**: {{no_window}}.
- **Melatonin**: {{dose_or_none}} ({{rationale_if_dosed}})
- **Wind-down**: {{wd_time}}

Apply the sleep-bank rule (R8.1) explicitly when relevant: if the night before flight forces an unusually early wake, the bedtime may move *against* the protocol direction.

## Phase 2 — Travel day

Detailed block-by-block walkthrough of departure morning, ground transit, in-flight, and arrival:

### Origin morning (pre-airport)

Time-stamped sequence of actions, light tier, caffeine, no melatonin unless protocol dictates.

### Flight {{flight_number}} {{origin_airport}} → {{dest_airport}} ({{depart_local}} → {{arrive_local}}, {{flight_duration}})

Two-block approach when applicable (R8.2):
- **First N hours / nap block (if applicable)**: light tier, caffeine, sleep guidance
- **Last N hours / main sleep block**: light tier, melatonin (in-flight dose for advance-direction return flights), eye mask

### After landing {{arrive_local}}

- Hourly blocks for the first ~8 hours covering: light tier, walking/outdoor exposure, no-nap rule, dinner, dim transition, melatonin (if applicable), wind-down, bedtime

## Phase 3 — Adaptation in destination ({{D+1}} to {{D+N}})

For each adaptation day until full adaptation:

### {{day_name}} {{date}} (D+{{n}})

- **Wake**: {{wake_time}} (anchor wake, R8.3)
- **Light tiers**:
  - Schedule by tier with destination clock times
- **Caffeine OK / No-caffeine block**
- **Melatonin**: {{dose_or_none}}
- **Wind-down**: {{wd_time}}
- **Bedtime**: {{bedtime}}

## Phase 4 — Pre-return (if round-trip)

Pre-shift starting ~2–3 days before return flight, in the opposite direction. Apply R8.1 sleep-bank rule, R6 melatonin advance/delay timing.

## Phase 5 — Return travel day

Same structure as Phase 2 but for the return leg. Highlight the in-flight melatonin dose timing for advance-direction flights (eastward returns from westward trips, etc.).

## Phase 6 — Recovery (post-return)

For each recovery day until expected full re-adaptation:

- **Wake**: anchor at home norm
- **Light tiers**: emphasize strong-seek on wake to entrain
- **Melatonin**: tapering dose for 2–3 nights, judgment call based on sleep quality (parameter quarantine — see algorithm-rules.md)
- **Caffeine cutoff**: keep early until sleeping through the night
- **Bedtime**: target home norm

## Cheat-sheets

### Outbound day (compact)

```
HH:MM ZONE  Action — light tier, caffeine, melatonin, sleep
HH:MM ZONE  ...
```

### Return day (compact, if round-trip)

```
HH:MM ZONE  ...
```

## Where this could be wrong

5–8 honest caveats specific to this trip:
- Phase marker uncertainty
- Aggressive single doses or unusual logistics
- Meeting/work overlap that fragments the protocol
- Light intensity assumption (outdoor vs indoor)
- Melatonin tolerance unknown if first-time user
- Wearable signal interpretation uncertainty
- Anything else trip-specific

## Citations

This plan applied:
- R1, R8.3 — Burgess 2014, Kantermann 2015
- R2, R3, R8.1, R11 — Eastman & Burgess 2009
- R4, R5 — Khalsa 2003, St Hilaire 2012, Zeitzer 2000
- R6 — Burgess 2010 (0.5 mg PRC); Burgess 2008 (3 mg PRC if dose ≠ 0.5 mg)
- R7, R13 — Burke 2015
- R8 — Borbély 1982
- R10 — wearable modulation (background literature: Plews 2013, Knufinke 2018)

Full bibliography in `references/science.md`. Per-paper extracts in `references/papers/`.

---

## After emitting the markdown above — render the chronotherapy chart

Call `mcp__visualize__show_widget` with an SVG following `templates/chart-schema.md`.

Required: do not skip this step. The chart is part of the output contract.
