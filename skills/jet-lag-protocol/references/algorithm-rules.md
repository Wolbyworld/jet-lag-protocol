# Algorithm rules — circadian phase-shift protocol

Every constant and decision rule in the algorithm, tagged to its source in `science.md`. When the science updates, edit this file and the updated rule propagates through the skill's reasoning.

Citation keys refer to entries in `science.md`.

---

## R1 — Phase markers from self-report

Estimate the user's current circadian phase from habitual bedtime, habitual wake, and chronotype.

```
chronotype ∈ {early, intermediate, late}
DLMO_offset = {early: 2.0 h, intermediate: 2.5 h, late: 3.0 h}[chronotype]

DLMO       = habitual_bedtime − DLMO_offset
DLMO_check = habitual_wake_time + 13.5 h
            # take mean if both available; flag if they disagree by >2 h
CBT_min    = DLMO + 7 h
            # cross-check: should fall ~1.5–2 h before habitual_wake_time
```

**Sources:** Burgess 2014 (DLMO from bedtime/wake regression); Kantermann et al. 2015 (chronotype-DLMO correlation, basis for offset table).

---

## R2 — Direction and magnitude of required shift

```
delta = destination_TZ − origin_TZ                # positive = eastward
if abs(delta) > 12: delta = -sign(delta) × (24 − abs(delta))
direction = "advance" if delta > 0 else "delay"
magnitude = abs(delta)                            # in hours
```

The 12-hour rule encodes that for very large eastward shifts, going around the long way (delay) is faster.

**Sources:** Eastman & Burgess 2009 (review of direction-decision logic).

---

## R3 — Adaptation rate and total days

```
rate_advance = 1.0 h/day      # with full intervention (light + melatonin + sleep)
rate_delay   = 1.5 h/day      # with full intervention

# Without intervention, real-world rates degrade to:
rate_advance_natural = 0.95 h/day   # ~57 min
rate_delay_natural   = 1.5 h/day    # ~92 min

days_to_adapt = ceil(magnitude / rate_for_direction)
```

**Sources:** Eastman & Burgess 2009 (intervention rates); Revell et al. 2006 (1 h/day advance with combined light+melatonin demonstrated).

**Asymmetry rationale:** human τ ≈ 24.2 h, slightly long, so the SCN drifts later naturally. Delays move with τ, advances against it.

---

## R4 — Light intensity model (4 tiers)

Discrete approximation of the Zeitzer 2000 sigmoid dose-response.

| Tier | Lux range (eye) | Practical example | PRC effect |
|---|---|---|---|
| **Strong-seek** | ≥5,000 | Direct outdoor sunlight | Saturated |
| **Mild-seek** | 200–1,000 | Bright indoor, near a window | ~50–80% of saturated |
| **Soft-avoid** | 10–100 | Dim indoor, sunglasses outdoors | Sub-half-saturation, near-threshold |
| **Full-avoid** | <2 | Eye mask, blackout, eyes closed | Zero functional dose |

**Source:** Zeitzer 2000 (threshold ~2 lux, half-saturation ~120 lux, saturation ~550 lux).

---

## R5 — Light windows for phase shift

CBT_min is the pivot point of the Khalsa light PRC. Light *before* CBT_min delays; light *after* CBT_min advances.

For **delay** (westward) protocol:
```
STRONG_SEEK   = [CBT_min − 5h, CBT_min − 2h]   # peak biological evening
MILD_SEEK     = [CBT_min − 8h, CBT_min − 5h]   # late afternoon body-time
SOFT_AVOID    = [CBT_min − 1h, CBT_min + 1h]   # peri-CBT_min
FULL_AVOID    = [CBT_min + 1h, CBT_min + 5h]   # biological early morning
```

For **advance** (eastward) protocol:
```
STRONG_SEEK   = [CBT_min + 2h, CBT_min + 5h]   # peak biological early morning
MILD_SEEK     = [CBT_min + 5h, CBT_min + 8h]   # mid-morning body-time
SOFT_AVOID    = [CBT_min − 1h, CBT_min + 1h]   # peri-CBT_min
FULL_AVOID    = [CBT_min − 5h, CBT_min − 1h]   # biological late evening
```

Each protocol day, after applying the day's shift, slide CBT_min forward (advance) or back (delay) by `rate_for_direction` hours and recompute the windows.

**Sources:** Khalsa et al. 2003 (PRC peak timing relative to CBT_min); St Hilaire et al. 2012 (1h-pulse PRC for shorter exposures); Zeitzer 2000 (intensity tiers).

### R5.1 — Practical translation (windows are theoretical, scheduling is real)

The windows in R5 come from constant-routine PRCs where the subject is awake 24/7. In a real plan, the user is asleep during much of the theoretical STRONG_SEEK window (e.g. for an early bird with CBT_min 03:30, STRONG_SEEK delay window is 22:30–01:30 — entirely during sleep).

Practical translation:

- **Pre-flight delay protocol**: get the brightest practical light tier in the period from late afternoon to ~30 min before bedtime. This overlaps with the early-edge of the theoretical STRONG_SEEK window once bedtime drifts later. *Don't* prescribe light during the theoretical peak (which is during sleep); prescribe light during the user's actual evening wake hours.

- **Pre-flight advance protocol**: get the brightest practical light tier from wake until ~5 h after wake. This overlaps with the theoretical STRONG_SEEK advance window.

- **Post-arrival**: light intensity tier guidance follows the user's actual wake/sleep schedule, not the theoretical PRC windows. This is captured in R8.3's wake-anchor rule.

The rule of thumb: prescribe **strong-seek on the side of the day farthest from the user's planned sleep** (evening for delay, morning for advance), accepting that the theoretical peak may be unreachable due to sleep timing.

---

## R6 — Melatonin dose timing

Default dose: **0.5 mg, immediate-release**, sublingual or oral. The 0.5 mg PRC is shifted ~1.5 h later than the 3.0 mg PRC and produces equivalent shifts when timed correctly, without daytime grogginess.

```
# 0.5 mg PRC
max_advance_dose_time = DLMO − 3.0 h       # equivalently, bedtime − 5.5 h
                                            # equivalently, CBT_min − 10.5 h
max_delay_dose_time   = CBT_min + 6.5 h    # equivalently, wake_time + 3.5 h
```

For **advance** (eastward) protocol:
```
pre_flight_dose     = bedtime[d] − 5.5 h     # afternoon at origin
return_flight_dose  = takeoff_time + ~1 h    # early in-flight, just before
                                              # the in-flight sleep window.
                                              # This places it ~3 h before
                                              # current DLMO (which has been
                                              # advancing during the day from
                                              # morning bright light) — at
                                              # the peak-advance point of
                                              # the 0.5 mg PRC.
post_arrival_dose   = bedtime[d] − 5.5 h     # tapered over 2–3 nights,
                                              # judgment call based on sleep
                                              # quality
```

For **delay** (westward) protocol:
```
in_flight_dose      = NONE                   # the literature is tepid on
                                              # westward melatonin; soporific
                                              # benefit doesn't justify the
                                              # awkward phase timing
arrival_night_dose  = bedtime_destination − 0.5 h   # small soporific dose to
                                                     # consolidate the new
                                                     # bedtime against the
                                                     # body's wake-maintenance
                                                     # zone
adaptation_doses    = OPTIONAL, 1–2 nights at bedtime − 0.5 h, drop if
                      sleep is good
```

**Sources:** Burgess et al. 2008 (3 mg PRC); Burgess et al. 2010 (0.5 mg PRC, the canonical timing); Burgess clinical handout (recovery taper guidance: 2–3 nights).

---

## R7 — Caffeine

```
half_life          = ~5 h        # median; range 2–10 h
phase_effect       = ~40 min delay per 200 mg dose given 3 h before bedtime
                                  # ~half the magnitude of 3 h evening bright
                                  # light at 3000 lux

# Cutoff timing differs by direction:
caffeine_cutoff_advance = bedtime[d] − 8 h   # earlier — caffeine would
                                              # phase-delay against the goal
caffeine_cutoff_delay   = bedtime[d] − 6 h   # more permissive — caffeine
                                              # mildly aligned with the
                                              # delay direction

# Output as paired blocks per day:
CAFFEINE_OK_block     = [wake_time, caffeine_cutoff]
NO_CAFFEINE_block     = [caffeine_cutoff, bedtime]
```

Dose guidance text accompanying the schedule: *"little and often"* — 20–50 mg every 1–2 h beats a single 200 mg slug. Total daily intake unchanged; sleep disruption reduced.

**Sources:** Burke et al. 2015 (phase effect, mechanism, half-life behavior).

---

## R8 — Sleep schedule

### R8.1 Pre-flight: the sleep-bank rule

Naive rule "drift bedtime 1 h/day in the protocol direction" is wrong when departure-day wake is unusually early (e.g. early feeder flight). Override with sleep-banking on the night before flight:

```
For each pre-flight day d:
    protocol_bedtime    = bedtime[d−1] + (sign × 1.0 h)
    sleep_bank_bedtime  = depart_day_wake_time − target_sleep_h
                                                   # e.g. 04:30 wake, 7h target → 21:30

    if direction == DELAY:
        bedtime[d] = min(protocol_bedtime, sleep_bank_bedtime)
                     # take the EARLIER — sleep-bank wins if morning forces it
    elif direction == ADVANCE:
        bedtime[d] = max(protocol_bedtime, sleep_bank_bedtime)
                     # take the LATER — sleep-bank wins if departure logistics
                     # require later sleep
```

Two-out from departure (D−2): typically protocol-shifted.
Night before (D−1): typically sleep-bank-dominated when the flight is early.
The flight's own sleep block carries the actual phase shift.

**Anti-reversal correction**: if D−1's sleep-bank bedtime is *opposite* the protocol direction (e.g. D−1 must be earlier than baseline due to early flight, but the protocol direction is delay/later), then D−2's protocol drift is wasted because D−1 reverses it. In that case, soften D−2's drift to 0.5 h (or skip entirely if magnitude is small):

```
if direction == DELAY and sleep_bank_bedtime[d=−1] < bedtime_baseline:
    bedtime[d=−2] = bedtime_baseline + 0.5h     # softened drift
elif direction == ADVANCE and sleep_bank_bedtime[d=−1] > bedtime_baseline:
    bedtime[d=−2] = bedtime_baseline − 0.5h     # softened drift
```

This avoids the "shift later then snap earlier" yo-yo, which produces sleep debt without phase benefit.

**Sources:** Borbély 1982 (Process S/C — wake-maintenance zone makes it impossible to fall asleep against the WMZ; sleep-bank bedtime must land at or after the sleep gate); Eastman & Burgess 2009 (caveats on sleep-debt cost of aggressive pre-flight shifts).

### R8.2 In-flight sleep — possibly two blocks

For long-haul flights:
```
if flight_duration > 9h and direction == ADVANCE:
    nap_block        = [takeoff + 1h, takeoff + 3h]      # ~2h short nap
    main_sleep_block = [mid_flight,   pre_landing − 1h]  # destination-night-aligned
elif flight_duration > 9h and direction == DELAY:
    # No early nap (would erase homeostatic pressure for destination bedtime).
    late_nap_block   = [pre_landing − 4h, pre_landing − 1h]
    # Main sleep happens at destination.
```

**Sources:** Borbély 1982 (homeostatic pressure management); Eastman & Burgess 2009 (in-flight sleep alignment to destination).

### R8.3 Post-arrival

Anchor the **wake time** to destination clock — wake time is the dominant entrainment cue. Bedtime[d] is whatever the user can fall asleep at; sleep duration may be short on early adaptation nights.

**Wake-anchor light rule**: get strong-seek light immediately on wake, every adaptation day, *regardless* of where wake falls relative to the still-shifting CBT_min. Rationale:

- The theoretical PRC says morning light during a delay protocol could counter-shift (advance the clock against the delay direction). In practice, the alertness benefit + entrainment to destination clock dominates the small advance contribution.
- The strong-seek dose first thing on wake also *resets the entrainment phase* — Roenneberg/MCTQ literature indicates wake-time light is the dominant zeitgeber for stable entrainment.
- For delay protocols, the strong-seek dose continues into late afternoon/evening as well; for advance protocols, it's concentrated in the morning only.

This rule is the practical compromise between pure-PRC theory and adherence: telling the user to "sleep in until 11 AM destination time and avoid morning light for 4 days" produces compliance failures that are worse than the small theoretical loss from morning light.

**Source:** Roenneberg et al. 2007 (entrainment via wake-time / first-light); pragmatic adaptation of Eastman & Burgess 2009 protocols.

---

## R9 — Wind-down marker

A 30–60 min discrete pre-bed event of dim lighting + screens off, regardless of phase-shift direction. Soporific aid for melatonin onset, lower light is sufficient (well below CBT-min-relative window).

```
WIND_DOWN_block = [bedtime[d] − 0.5 h, bedtime[d]]
```

**Sources:** general sleep-hygiene literature (not jet-lag-specific; included because it strictly improves outcomes without phase-shift cost).

---

## R10 — Sleep-quality modulation (input from any wearable)

The skill consumes generic signals from whichever data source the user has connected — Garmin, Apple Watch (HealthKit), Oura, Whoop, Withings, manual self-report. Map vendor-specific fields to these abstract signals:

| Abstract signal | Source examples |
|---|---|
| 7-day mean sleep duration | All sources |
| 7-day sleep score / quality | Garmin sleep score, Oura sleep score, Whoop sleep performance, Withings sleep score, Apple Watch (computed from continuity) |
| HRV trend vs baseline | rMSSD or vendor HRV; Apple Watch via HealthKit; Oura, Whoop, Garmin natively |
| RHR trend vs baseline | All sources |
| Recovery proxy | Garmin body battery; Oura readiness; Whoop recovery %; Apple Watch (computed); manual self-report |

```
# Modulation rules (apply on top of the protocol's nominal intensity):

if 7d_sleep_score < 70 OR HRV_trend < −10% baseline:
    pre_flight_drift_rate *= 0.5     # soften pre-flight to 0.5 h/day
    in_flight_nap_aggressiveness *= 0.7

if RHR_trend > +5 bpm vs baseline:
    flag_to_user("RHR elevated — possible illness/recovery debt; consider
                 delaying optional pre-shift component")

if no recent sleep deficit (all signals at or above baseline):
    run protocol at full nominal intensity
```

**Fallback if no wearable connected:**
```
ask: "How was your sleep last week — good / mixed / poor?"
  good  → full intensity
  mixed → soften by 25%
  poor  → soften by 50%, flag user
```

**Sources:** Plews et al. 2013 (HRV-based training prescription); Knufinke et al. 2018 (single-night sleep deficit effects).

---

## R11 — Trip-length minimums

```
if magnitude < 3 hours AND trip_length < 3 nights:
    skip protocol
    output: "Stay on home time — adaptation cost > jet-lag cost for short trips."
```

**Source:** Eastman & Burgess 2009 (cost-benefit of pre-shifting for short trips).

---

## R12 — Older travelers

PRC amplitude shrinks ~25% in adults >55. Scale `rate_for_direction` accordingly when computing total days.

**Source:** Klerman et al. 2007 (J Circadian Rhythms — age-related PRC amplitude decline).

---

## R13 — Caffeine sensitivity (slow metabolizers)

If user reports slow caffeine metabolism (CYP1A2 *1F/*1F genotype, hormonal contraceptives, pregnancy, smoking cessation), extend the no-caffeine block:

```
caffeine_cutoff_advance_slow = bedtime[d] − 10 h
caffeine_cutoff_delay_slow   = bedtime[d] − 8 h
```

**Source:** Burke et al. 2015 (variable half-life context).

---

## Parameter quarantine

These values are educated guesses, not strongly evidence-backed at current N. Surface to user if asked, but treat as defaults rather than facts:

- **Number of recovery melatonin nights**: default 2–3, taper by sleep quality. Burgess handout endorses 2–3, no clean trial data on which.
- **Pre-flight days**: 2 days for both directions is the default. Some literature suggests 3 for advance shifts ≥6 h. Could vary by magnitude.
- **Two-block in-flight sleep threshold**: "flight_duration > 9h" is a heuristic, not literature-bound.
- **Soft-avoid lux range upper bound (100 lux)**: chosen at half-saturation point; could be lower depending on individual sensitivity.

When a future trip's outcome contradicts a quarantined value, the value updates. Until then, hold defaults.
