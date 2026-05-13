# Algorithm rules — circadian phase-shift protocol

Every constant and decision rule in the algorithm, tagged to its source in `science.md`. When the science updates, edit this file and the updated rule propagates through the skill's reasoning.

Citation keys refer to entries in `science.md`.

**Calibration date:** 2026-05-13 (v0.3.0 — science-integrity pass; see changelog at end of file).

---

## Evidence policy

Every prescription in this file is classified as one of:

1. **Evidence-backed circadian rule** — supported by peer-reviewed literature or official clinical guidance (CDC Yellow Book, sleep-medicine reviews).
2. **Engineering heuristic** — plausible and useful, but the exact numeric threshold or formula is not directly validated. Listed in the Parameter quarantine appendix at the bottom of this file.
3. **UX copy / adherence aid** — helpful behavior framing, but not a physiological claim.

Heuristics must not masquerade as literature-derived constants. When a rule cites a paper, the citation supports the *qualitative* mechanism, not necessarily the exact numeric default. The Parameter quarantine appendix is the source of truth for "what's heuristic."

Sources are limited to academic literature and official clinical guidance. Blogs, wellness sites, consumer-product marketing, and unsourced "sleep hygiene" pages are not citable.

---

## R1 — Phase markers from self-report

Estimate the user's current circadian phase from habitual bedtime, habitual wake, and chronotype.

```
chronotype ∈ {early, intermediate, late}
DLMO_offset = {early: 2.0 h, intermediate: 2.5 h, late: 3.0 h}[chronotype]   # heuristic

DLMO       = habitual_bedtime − DLMO_offset
DLMO_check = habitual_wake_time + 13.5 h
            # take mean if both available; flag if they disagree by >2 h
CBT_min    = DLMO + 7 h
            # cross-check: should fall ~1.5–2 h before habitual_wake_time
```

The DLMO offset table is an **engineering heuristic** — Burgess 2014 and Kantermann 2015 establish the chronotype-DLMO correlation qualitatively, but the exact 2.0 / 2.5 / 3.0 hour offsets are calibrated defaults, not regression-derived constants. See Parameter quarantine.

**Sources:** Burgess 2014 (DLMO from bedtime/wake regression); Kantermann et al. 2015 (chronotype-DLMO correlation).

---

## R2 — Direction and magnitude of required shift

```
delta = destination_TZ − origin_TZ                # positive = eastward
direction = "advance" if delta > 0 else "delay"
magnitude = abs(delta)                            # in hours

# Coarse default: for shifts greater than 12h, going the long way around is faster.
if magnitude > 12:
    direction = flip(direction)
    magnitude = 24 − magnitude
```

For large eastward shifts (≥ 8h advance), the simple 12 h flip is a coarse default. The richer rule evaluates both directions and picks based on predicted adaptation days plus adherence/light-control feasibility:

```
# Heuristic — evaluate-both for borderline advances.
if direction == "advance" and magnitude >= 8:
    candidate_advance = (advance, magnitude,           rate_advance)
    candidate_delay   = (delay,   24 − magnitude,      rate_delay)
    pick whichever:
        - fewer predicted days_to_adapt
        - feasible light/behavior control (red-eye departures, etc.)
        - lower expected sleep debt
```

The `magnitude >= 8` trigger is an engineering heuristic; the 12 h flip itself is heuristic too. Both go in Parameter quarantine.

**Sources:** Eastman & Burgess 2009 (review of direction-decision logic); CDC Yellow Book 2026 (jet-lag direction-and-magnitude guidance).

---

## R3 — Adaptation rate and total days

```
rate_advance = 1.0 h/day      # with full intervention (light + melatonin + sleep)
rate_delay   = 1.5 h/day      # with full intervention

days_to_adapt = ceil(magnitude / rate_for_direction)
```

**Asymmetry rationale:** human τ ≈ 24.2 h, slightly long, so the SCN drifts later naturally. Delays move with τ, advances against it.

**R3 does NOT scale by age.** See R12 — Kripke 2007 did not support a universal age-based PRC amplitude reduction; the prior `× 0.75` for adults >55 has been removed.

The rate constants themselves are intervention-supported (Revell 2006 for advance with combined light+melatonin) but the exact 1.0 / 1.5 figures are engineering defaults. Quarantined.

**Sources:** Eastman & Burgess 2009 (intervention rates); Revell et al. 2006 (1 h/day advance with combined light+melatonin); Kripke et al. 2007 (no universal age effect on light PRC).

---

## R4 — Light intensity model

Discrete approximation of the dose-response, expressed in **melanopic equivalent daylight illuminance (melanopic EDI)** per Brown et al. 2022. This replaces simple photopic lux.

| Tier | Melanopic EDI | Practical example | Use |
|---|---|---|---|
| **Strong-seek** | ≥ 250 | Direct outdoor sunlight; well-lit office near window; 10,000 lux therapy box | Daytime, on the right PRC limb for the protocol direction. |
| **Mild-seek** | 50–250 | Bright indoor; well-lit room mid-day | Maintenance / acceptable adherence floor. |
| **Soft-avoid** | 10–50 | Dim indoor; warm-white lamp; sunglasses outdoors; screens night-shift | Evening, or when wake falls on the wrong PRC limb. |
| **Dim-safe / evening-target** | 1–10 | Single warm bulb at <30%; ambient candlelight; reading lamp | Final 3 h before bed; Brown 2022 evening target. |
| **Full-avoid / sleep** | < 1 | Eye mask; blackout; eyes closed | Sleep window. |

The previous Soft-avoid floor of `<1` left a 1–10 EDI gap; "Dim-safe / evening-target" closes it.

**Sources:**
- Brown et al. 2022 — melanopic EDI thresholds: daytime ≥250, evening ≤10 melanopic EDI starting at least 3 h before bedtime, sleep ≤1.
- Phillips et al. 2019 — population ED50 in melatonin suppression ~25 photopic lux ≈ ~14 melanopic EDI; individual range 6–350 lux (>50× spread). Measured melatonin suppression / sensitivity, NOT validated jet-lag phase-shift tier system.
- Zeitzer 2000 — historical baseline (superseded by Brown 2022 for prescriptions).

### R4.1 — Melanopic Daylight Efficacy Ratio (DER) conversion

When a user reports light exposure in photopic lux (phone meters, store-listed bulb ratings), convert before tier classification:

```
melanopic_EDI ≈ photopic_lux × DER

# DER ranges, by light source spectrum:
warm LED / incandescent (2700–3000K):  DER ≈ 0.30 – 0.45
neutral LED (3500–4000K):              DER ≈ 0.50 – 0.70
daylight LED (5000–6500K):             DER ≈ 0.80 – 0.90
daylight / blue sky:                   DER ≈ 1.0 – 1.2
```

**Conservative fallback when source spectrum is unknown** (the direction matters):

```
if goal == "avoid_light_evening":
    # Conservative = assume MORE melanopic EDI than the photopic meter suggests
    estimated_melanopic_EDI = photopic_lux × upper_bound_DER
elif goal == "seek_light_daytime":
    # Conservative = assume LESS melanopic EDI than hoped
    estimated_melanopic_EDI = photopic_lux × lower_bound_DER
```

The DER ranges are engineering heuristics; the exact spectrum-to-DER mapping depends on the bulb and viewer geometry. Quarantined.

### R4.2 — Individual light sensitivity (optional personalization)

Per Phillips 2019, ED50 in melatonin suppression ranges from 6 lux (most sensitive) to 350 lux (least sensitive) — >50× individual variability. Three-bucket approximation:

```
sensitivity ∈ {sensitive, average, resistant}     # default: average

if sensitivity == "sensitive":
    soft_avoid_upper  *= 0.4                       # heuristic
    strong_seek_lower *= 0.5                       # heuristic
elif sensitivity == "resistant":
    soft_avoid_upper  *= 1.5                       # heuristic
    strong_seek_lower *= 2.0                       # heuristic
```

Phillips 2019 measured melatonin suppression / individual sensitivity, NOT a validated phase-shift tier system. The 0.4 / 0.5 / 1.5 / 2.0 multipliers are engineering heuristics — quarantined.

Detect sensitivity from (a) explicit user self-report, or (b) feedback from prior protocols. Default to "average" if no signal.

---

## R5 — Light windows for phase shift

CBT_min is the pivot point of the Khalsa light PRC. Light *before* CBT_min delays the clock; light *after* CBT_min advances it.

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

**Sources:** Khalsa et al. 2003 (PRC peak timing relative to CBT_min); St Hilaire et al. 2012 (1 h-pulse PRC for shorter exposures).

### R5.1 — Practical translation (windows are theoretical, scheduling is real)

The windows in R5 come from constant-routine PRCs where the subject is awake 24/7. In a real plan, the user is asleep during much of the theoretical STRONG_SEEK window.

Practical translation:

- **Pre-flight delay protocol:** get the brightest practical light tier in the period from late afternoon to ~30 min before bedtime. *Don't* prescribe light during the theoretical peak (which is during sleep); prescribe during the user's actual evening wake hours.
- **Pre-flight advance protocol:** get the brightest practical light tier from wake until ~5 h after wake.
- **Post-arrival:** behavior (wake time, meals, social activity) follows the destination schedule, but **light remains PRC-gated using the current phase estimate** — see R8.3. The previous wording "light intensity tier guidance follows the user's actual wake/sleep schedule, not the theoretical PRC windows" was too broad and contradicted R8.3.

The rule of thumb: prescribe **strong-seek on the side of the day farthest from the user's planned sleep** (evening for delay, morning for advance), accepting that the theoretical peak may be unreachable due to sleep timing.

### R5.2 — In-flight primary sleep block

For any travel day where:

```
magnitude          ≥  6 h                         # heuristic
longest_leg.overlaps(destination_night)  ≥  3 h    # heuristic
```

emit a **recommended primary in-flight sleep block** anchored to destination night, direction-specific:

```
delay   (westward shift):
    block_start = max(longest_leg.depart + arrival_buffer, dest_night_start)
    block_end   = min(block_start + block_target,
                      longest_leg.arrival − arrival_buffer,
                      dest_night_end + creep)

advance (eastward shift):
    block_end   = min(longest_leg.arrival − arrival_buffer, dest_night_end)
    block_start = max(block_end − block_target,
                      longest_leg.depart + arrival_buffer,
                      dest_night_start − creep)
```

#### Quarantined R5.2 constants

| Constant | Default | Note |
|---|---|---|
| Magnitude threshold | 6 h | Below this the in-flight block isn't worth the disruption. |
| Destination-night overlap threshold | 3 h | Below this the flight timing can't carry destination-night sleep. |
| `block_target` | 6 h | Cap. Roach & Sargent 2019 supports ~5–7 h main sleep. |
| `arrival_buffer` | 60 min | Wake before descent / climb-out; don't sleep through pushback. |
| `creep` (past dest-night boundary) | 90 min | Sleep-bank extension when leg time permits (Burgess Penn CBTI 2020). |

All five are engineering heuristics. See Parameter quarantine.

#### Framing

This is a **recommended primary sleep opportunity**, not a guaranteed physiological event. Copy must not call it "mandatory" or "must sleep." If the user can't fall asleep, the rest of the protocol still works.

#### R5.2 emits a sleep opportunity. R6 handles melatonin independently.

R5.2 does NOT prescribe melatonin. The melatonin dose, if any, is governed by R6's phase-relative rule (`current_DLMO_proxy − 3 h`). Do not couple melatonin to `block_start − 15 min` or any other sleep-block-relative anchor.

#### Early "homeostatic-clearance nap" is dropped when R5.2 fires

The pre-R5.2 protocol emitted a 2 h nap at `[takeoff + 1h, takeoff + 3h]` on advance long-haul. That action is **removed** when R5.2 fires:

- On advance: discharging sleep pressure before the main block makes the main block harder to fall into (Burgess Penn CBTI 2020 — naps within 6 h of target bedtime delay onset).
- On delay: same homeostatic-pressure preservation argument that already excluded the early nap on the pre-R5.2 delay path.

#### Emergency nap escape hatch

If the user reports unsafe sleepiness on a travel day where R5.2 has emitted a primary block:

```
if unsafe_sleepiness:
    allow emergency_nap ≤ 20–30 min
    require nap_end ≥ 6 h before main_sleep_block_start
```

Sources: Roach & Sargent 2019 (in-flight sleep alignment on long-haul advance/delay); Eastman & Burgess 2009 §4 (in-flight scheduling); Burgess Penn CBTI 2020 (nap-pressure framing).

### R5.3 — Destination-aligned meal anchors

Meal timing is a **non-light timing cue for peripheral clocks** — liver, gut, glucose, adipose. Eating on destination time may reduce metabolic / GI desynchrony during the first adaptation days. It is **not** a substitute for correctly timed light, and it does **not** shift the master circadian clock.

```
# Defaults (heuristic), in destination local time:
breakfast = 07:30
lunch     = 12:30
dinner    = 19:30

# Emit on:
# - travel day (anchored to LANDING destination date)
# - DEST D+1, D+2, D+3
# Trigger:
emit if magnitude ≥ 6 h
```

Suppression:

```
suppress meal if:
    meal_time overlaps in_flight_sleep_block
    meal_time ∈ [22:00, 05:00) destination local         # dead zone
    meal_time within 90 min before main_sleep_block      # dinner only
```

**Macro guidance** (UX copy, NOT a circadian rule):

- Breakfast: light, protein-forward if appetite allows.
- Lunch: normal balanced meal.
- Dinner: avoid very heavy meals close to sleep.

The "protein → tyrosine → dopamine for alertness" / "carbs → tryptophan → serotonin → melatonin" framings are **not** science. Treat them at most as lifestyle suggestions.

The default meal times (07:30 / 12:30 / 19:30), the magnitude trigger (≥ 6 h), and the 90 min dinner-before-sleep gap are engineering heuristics. Quarantined.

**Sources:** Wehrens et al. 2017 (delayed meal timing shifted plasma glucose rhythms and adipose PER2 expression; master-clock markers — melatonin, cortisol, sleepiness — were NOT shifted); Vetter 2017 commentary (meal timing selectively uncouples metabolic rhythms from the central clock).

---

## R6 — Melatonin dose timing

**Default dose:** 0.5 mg, **oral immediate-release**.

Sublingual is *not* a supported delivery route for the timing rules below — Burgess et al. 2010 generated the 0.5 mg PRC with oral immediate-release; sublingual pharmacokinetics differ and would need its own PRC source. If the user prefers sublingual, the algorithm should fall back to a soporific-only mode.

### Math, anchored to phase

```
# R1 defines CBT_min = DLMO + 7 h. Therefore:
target = current_DLMO − 3 h
       = CBT_min − 10 h
       # NOT "CBT_min − 10.5 h" and NOT "bedtime − 5.5 h" (the latter is
       # only true for the intermediate-chronotype DLMO offset).

valid_window = [current_DLMO − 4 h, current_DLMO − 2 h]
```

For chronotypes other than intermediate, bedtime-anchored shortcuts diverge:

```
early:        DLMO − 3 h = bedtime − 5.0 h
intermediate: DLMO − 3 h = bedtime − 5.5 h
late:         DLMO − 3 h = bedtime − 6.0 h
```

**Always compute from `current_DLMO − 3 h`. Never from `bedtime − 5.5 h`.**

### Current_DLMO state machine

The current phase changes as the protocol progresses. The dose timing must follow:

```
current_DLMO_proxy = baseline_DLMO + signed_shift_already_accomplished_h

# signed: negative for advance (DLMO has shifted earlier), positive for delay.
# Each protocol day adds rate_for_direction * sign to the accumulator,
# clamped at signed magnitude (no overshoot past convergence).
```

### Advance (eastward) protocol

```
target = current_DLMO_proxy − 3 h

if target overlaps feasible flight/awake window:
    emit 0.5 mg oral immediate-release melatonin at target
elif valid_window overlaps feasible flight/awake window:
    emit at the point in the overlap closest to target
else:
    suppress in-flight phase-shift melatonin
```

The dose is **not** anchored to:

- `takeoff_time + 1 h` (the old pre-review rule — wrong, ignores phase).
- `sleep_block_start − 15 min` (the post-PR-10 rule — wrong, conflates chronobiotic with soporific).
- `bedtime − 5.5 h` (only correct for intermediate chronotype).

Post-arrival tapering of 2–3 nights at `current_DLMO − 3 h` is optional, judgment-based on sleep quality. The number of taper nights (2–3) is an engineering heuristic.

### Delay (westward) protocol

```
in_flight_phase_shift_dose = NONE
# DLMO − 3 h sits in the advance lobe of the Burgess 2010 PRC — emitting
# it on a delay protocol would push the wrong limb. The westward
# literature on melatonin is tepid and does not justify the awkward
# phase timing.

# Optional sleep-aid only:
arrival_night_soporific = bedtime_destination − 30 min  # NOT phase-shift
adaptation_night_soporific = OPTIONAL × 1–2 nights      # drop if sleep is good
```

If a destination-night in-flight sleep block exists (R5.2) and an optional sleep-aid melatonin action is desired, it must be **labelled as a sleep aid, not a phase-shift dose**. The default-on/off for this optional soporific is a product decision; literature is silent.

### Sedating-medication / alcohol suppression

If the user discloses any of:

- dimenhydrinate (Dramamine / Biodramina)
- diphenhydramine (Benadryl)
- doxylamine (Unisom)
- benzodiazepines / Z-drugs (alprazolam, zolpidem, etc.)
- alcohol ≥ 2 drinks during the flight
- any user-disclosed other sedative

then:

```
suppress in-flight melatonin (both phase-shift and soporific)
emit alertness note
do not encourage stacking sedatives
```

**Sources:** Burgess et al. 2010 (0.5 mg oral immediate-release PRC, DLMO-relative timing); Burgess et al. 2008 (3 mg PRC); CDC Yellow Book 2026 (jet-lag melatonin — internal-phase-based planning, 0.5–1 mg often sufficient).

---

## R7 — Caffeine

### Bed-anchored cutoff (non-travel days)

```
caffeine_cutoff_advance = bedtime[d] − 8 h    # heuristic
caffeine_cutoff_delay   = bedtime[d] − 6 h    # heuristic

CAFFEINE_OK_block = [wake_time, caffeine_cutoff]
NO_CAFFEINE_block = [caffeine_cutoff, bedtime]
```

The 8 h / 6 h offsets are engineering heuristics. Caffeine's half-life is ~5 h (range 2–10 h) — the cutoff is meant to clear *residual* stimulation by bedtime, NOT a measured "peak inhibition" threshold. CDC Yellow Book 2026 gives a practical "stop 6 h before bedtime" rule. Quarantined.

### Travel-day cutoff (when R5.2 fires)

When R5.2 emits a primary in-flight sleep block, the cutoff anchors to the block, not home bedtime:

```
travel_day_cutoff = in_flight_sleep_block.start − 8 h

if travel_day_cutoff < wake_time:
    emit "No caffeine today."   # cutoff before wake — no window to drink
else:
    emit "Last caffeine by {travel_day_cutoff}."
```

The `−8 h` offset is the same heuristic as the bed-anchored cutoff.

### Rationale copy

```
Caffeine after this may leave enough residual stimulation during the
in-flight sleep block (or at bedtime) to reduce sleep depth and delay
sleep onset.
```

Do **not** use:

```
"Caffeine after this reaches peak inhibition exactly when you need to sleep."
```

This is pharmacologically wrong — caffeine does not peak ~8 h after consumption.

### Dose guidance (UX copy)

*"Little and often"* — 20–50 mg every 1–2 h beats a single 200 mg slug. Total daily intake unchanged; sleep disruption reduced. This is a usability tip, not a circadian rule.

### Post-landing caffeine with sedating-medication disclosure

When `sedatingMedOnFlight` is disclosed (see R6), the post-landing (DEST D+1) caffeine action's rationale appends a partial-alertness caveat:

```
"Coffee may help alertness, but it does not fully reverse sedating-
medication impairment. Avoid driving or high-stakes work until you
feel clear."
```

Caffeine partially counters sedating-antihistamine drowsiness in measured-vigilance tasks, but it does **not** fully reverse impairment. The framing must be "partial alertness support," never "reverses the drug."

**Sources:** Burke et al. 2015 (caffeine's ~40 min phase-delay effect from a 200 mg dose given 3 h before habitual bedtime; ~5 h half-life median); CDC Yellow Book 2026 (practical "stop 6 h before bedtime" guidance, and short-nap + caffeine combination for managing alertness during transit).

---

## R8 — Sleep schedule

### R8.1 — Pre-flight: the sleep-bank rule (direction-agnostic)

Naive rule "drift bedtime 1 h/day in the protocol direction" is wrong when departure-day wake is unusually early. Override with sleep-banking on the night before flight:

```
For each pre-flight day d:
    protocol_bedtime    = bedtime[d−1] + (sign × rate_for_direction)
    latest_for_target_sleep = depart_day_wake_time − target_sleep_h

    # Direction-AGNOSTIC: bedtime cannot land later than the latest time
    # that still permits target sleep duration.
    bedtime[d] = min(protocol_bedtime, latest_for_target_sleep)
```

The previous direction-branched `max(protocol, sleep_bank)` for advance silently skipped sleep-banking on early-flight advance trips. Direction-agnostic `min()` covers both cases.

**Wake-maintenance-zone caveat:**

```
if latest_for_target_sleep lands inside the wake_maintenance_zone:
    emit lower-confidence copy:
        "This is earlier than your body is likely to fall asleep easily.
         Treat it as quiet, dark rest; sleep may not come immediately."
```

**Anti-reversal correction:** if D−1's sleep-bank bedtime would move *against* the protocol direction (early flight on a delay protocol, late flight on an advance), soften D−2's drift to 0.5 × `rate_for_direction` (or skip if magnitude is small) to avoid the "shift later then snap earlier" yo-yo. The 0.5× softening factor is heuristic.

**Sources:** Borbély 1982 / Borbély et al. 2022 (two-process sleep model — wake-maintenance zone overrides homeostatic pressure; sleep-bank bedtime must land at or after the sleep gate); Eastman & Burgess 2009 (caveats on sleep-debt cost of aggressive pre-flight shifts); CDC Yellow Book 2026 (sleep loss during travel worsens jet-lag symptoms; longer daytime naps interfere with subsequent nighttime sleep).

### R8.2 — In-flight sleep — deferred to R5.2

The previous two-block rule

```
if flight_duration > 9h and direction == ADVANCE:
    nap_block        = [takeoff + 1h, takeoff + 3h]
    main_sleep_block = [mid_flight,   pre_landing − 1h]
elif flight_duration > 9h and direction == DELAY:
    late_nap_block   = [pre_landing − 4h, pre_landing − 1h]
```

is **deprecated** and replaced by R5.2 — In-flight primary sleep block (above). When R5.2 fires, the early 2 h "homeostatic-clearance nap" is NOT emitted. The emergency-nap escape hatch (≤ 20–30 min, ending ≥ 6 h before the main sleep block) remains available for unsafe sleepiness.

The `flight_duration > 9 h` threshold is an engineering heuristic. Quarantined.

### R8.3 — Post-arrival: behavior anchor + PRC-gated light

**Wake time is the behavioral anchor.** Anchor wake, meals, social activity, movement, and local routine to the destination clock. Wake time is *not* the dominant entrainment cue — that phrasing overstates the Roenneberg/MCTQ framing.

**Light is the circadian phase-shifting anchor**, and it remains PRC-gated. Bright light after the circadian nadir (CBT_min) promotes phase advances; bright light before CBT_min promotes phase delays. Mistimed light can push the clock the wrong way.

```
wake_time = destination_anchor_wake

# Behavior anchor (always, regardless of PRC limb):
anchor wake, meals, social activity, movement, and local routine
to destination clock.

# Light prescription (PRC-gated):
if direction == "advance":
    if wake_time_body_time < estimated_CBT_min + 1 h:
        # Pre-CBT phase-delay region — bright light here would push the
        # wrong limb.
        emit soft-avoid on wake (sunglasses / dim indoor light)
        emit strong-seek at estimated_CBT_min + 2 h
    else:
        emit strong-seek on wake

if direction == "delay":
    if estimated_CBT_min + 1 h ≤ wake_time_body_time ≤ estimated_CBT_min + 5 h:
        # Post-CBT phase-advance lobe — bright light here advances the
        # clock against the delay protocol.
        emit soft- or full-avoid on wake
        emit strong-seek in late afternoon / evening
    else:
        emit strong-seek on wake (right limb)
```

**UX copy on the soft-avoid path:**

```
"Your body clock is still in the wrong part of the light-response curve.
Use dim indoor light or sunglasses now; get strong outdoor light later
today."
```

The previous wording — "get strong-seek light immediately on wake, every adaptation day, **regardless of where wake falls relative to the still-shifting CBT_min**" — is **removed**. It silently violated the Khalsa PRC and pushed the wrong direction on early-adaptation days.

**Sources:** Khalsa et al. 2003 (human bright-light PRC under controlled conditions — delays when light centered before CBT_min, advances after); CDC Yellow Book 2026 (timing direction-dependent; mistimed light can push the wrong way); Roach & Sargent 2019 (applied jet-lag protocols, direction-specific avoid windows).

---

## R9 — Wind-down marker

Two-stage prescription: a hard minimum and a fuller target.

```
WIND_DOWN_block_minimum = [bedtime[d] − 0.5 h, bedtime[d]]   # the hard action
WIND_DOWN_block_target  = [bedtime[d] − 3.0 h, bedtime[d]]   # the full target
```

- **Minimum** (30 min): switch off bright lights, screens off or night-shift, low-melanopic ambient. Renderable as one explicit action.
- **Target** (3 h): keep ambient light below 10 melanopic EDI for the final 3 h before bed. Renderable as guidance copy on the same action.

```
UX copy:
"The hard action is the final 30 minutes — dim screens, warm bulbs only,
no kitchen overhead. The full target is to keep ambient light below 10
melanopic EDI for the final 3 hours before bed."
```

**Sources:** Brown et al. 2022 (consensus recommendation: evening melanopic EDI ≤ 10 starting at least 3 h before bedtime).

---

## R10 — Sleep-quality modulation (wearable input)

The skill consumes generic signals from whichever data source the user has connected — Garmin, Apple Watch (HealthKit), Oura, Whoop, Withings, manual self-report. Map vendor-specific fields to abstract signals:

| Abstract signal | Source examples |
|---|---|
| 7-day mean sleep duration | All sources |
| 7-day sleep score / quality | Garmin sleep score, Oura sleep score, Whoop sleep performance, Withings sleep score, Apple Watch (computed from continuity) |
| HRV trend vs personal baseline | rMSSD or vendor HRV; Apple Watch via HealthKit; Oura, Whoop, Garmin natively |
| RHR trend vs personal baseline | All sources |
| Recovery proxy | Garmin body battery; Oura readiness; Whoop recovery %; Apple Watch (computed); manual self-report |

**Evidence-backed framing:** use wearable sleep/wake duration and trends *relative to personal baseline*. Do **not** rely on absolute sleep-stage durations (deep / REM) for protocol decisions; consumer trackers are inconsistent versus polysomnography.

**Quarantined modulation thresholds:** all of the following are engineering heuristics, not validated by Chinoy 2021 or any RCT.

```
# Heuristic — quarantined.
if 7d_sleep_score < (personal_30d_sleep_score_avg − 1 SD)
   OR HRV_trend < −10% personal baseline:
    pre_flight_drift_rate *= 0.5
    in_flight_nap_aggressiveness *= 0.7

if RHR_trend > +5 bpm vs personal baseline:
    flag_to_user("RHR elevated — possible illness/recovery debt; consider
                 delaying optional pre-shift component")

if all signals at or above personal baseline:
    run protocol at full nominal intensity
```

**Do NOT use:**

- absolute sleep-score thresholds (e.g. "< 70") — vendor scales don't translate.
- sleep-stage data (deep / REM absolute durations) — accuracy is too poor.

**Fallback if no wearable connected** (also heuristic):

```
ask: "How was your sleep last week — good / mixed / poor?"
  good  → full intensity         (0% softening)
  mixed → 25% softening
  poor  → 50% softening, flag user
```

**Sources:** Chinoy et al. 2021 (validated 7 consumer trackers vs PSG — total sleep time ±15–30 min across vendors; sleep-stage data substantially error-prone; worse on poor / disrupted nights).

---

## R11 — Trip-length minimums (trip-length primary)

```
if trip_nights ≤ 2:
    skip full adaptation protocol
    output:
        "Stay closer to home time — trip is too short to benefit from
         full circadian adaptation. Use naps and caffeine for alertness."

elif trip_nights == 3 and magnitude < 5 h:
    emit minimal protocol with warning:
        "Expected benefit is marginal. Use light / caffeine / sleep tactics
         for alertness rather than full phase shifting."

elif magnitude < 3 h and trip_nights < 5:
    emit minimal protocol with note that pre-shifting is optional.

else:
    full protocol.
```

Trip-length is the **primary** gate. The previous combined rule (`magnitude < 3 h AND trip_length < 3 nights`) was wrong: a 2-night LA→NYC trip with 3 h shift was still recommended a full protocol despite there being no benefit.

The exact gate values (2 nights / 3 nights & 5 h / 5 nights & 3 h) are engineering heuristics. Quarantined.

**Sources:** Arendt 2009 (for 1–2 day stopovers, adapting the circadian system is not advised; short-term measures such as naps and caffeine are preferred); Eastman & Burgess 2009 (cost-benefit of pre-shifting for short trips).

---

## R12 — Older travelers

**Age does NOT scale `rate_for_direction`.** The previous `× 0.75` reduction for adults > 55 is removed. Kripke et al. 2007 tested 3000 lux light PRCs in older and younger adults; maximal phase shifts were ~3 h and did **not** differ significantly in amplitude between groups. The difference was in *timing*, not a universal amplitude / rate reduction.

```
if age ≥ 55:
    phase_estimate_confidence *= 0.8

    ask_or_infer:
        cataract_or_AMD
        low_outdoor_light_routine
        low_morning_light_access
        unusually early chronotype

    if cataract_or_AMD or low_outdoor_light_routine:
        prefer outdoor strong-seek over indoor
        extend strong-seek window by 30 min      # heuristic
        widen phase uncertainty band

# NO automatic rate_for_direction scaling.
```

The 0.8 confidence multiplier and the 30 min strong-seek extension are engineering heuristics. Quarantined.

Brown et al. 2022 notes that older adults can have reduced effective retinal dose because of lens transmission changes, so the age rule focuses on **retinal light delivery**, not phase-shift-rate slowdown.

**Sources:** Kripke et al. 2007 (no universal age effect on light PRC); Brown et al. 2022 (lens transmission decline reduces retinal melanopic input with age).

---

## R13 — Caffeine sensitivity (slow metabolizers)

If the user reports slow caffeine metabolism (CYP1A2 *1F/*1F genotype, hormonal contraceptives, pregnancy, smoking cessation), extend the no-caffeine block:

```
caffeine_cutoff_advance_slow = bedtime[d] − 10 h     # heuristic
caffeine_cutoff_delay_slow   = bedtime[d] − 8 h      # heuristic
```

Active smoking increases caffeine clearance; smoking cessation makes caffeine last longer — both shift the effective half-life. The exact `+ 2 h` extension is an engineering heuristic.

**Sources:** Burke et al. 2015 (variable caffeine half-life context); CYP1A2 pharmacogenomics literature for genotype effects.

---

## Parameter quarantine

These are engineering heuristics — useful defaults, but **not directly literature-derived**. Annotation for each:

> Engineering heuristic, not directly literature-derived. Keep as default unless outcome data or future trials justify adjustment.

### R1 chronotype offsets
- `early: 2.0 h`, `intermediate: 2.5 h`, `late: 3.0 h`

### R2 direction-decision heuristic
- 12 h flip threshold.
- `magnitude ≥ 8 h` trigger for "evaluate-both directions."

### R3 adaptation rates
- `rate_advance = 1.0 h/day`, `rate_delay = 1.5 h/day`.

### R4 / R4.1 / R4.2 light model
- DER conversion ranges (warm LED 0.30–0.45, neutral LED 0.50–0.70, daylight LED 0.80–0.90, daylight 1.0–1.2).
- R4.2 sensitivity multipliers: 0.4 / 0.5 / 1.5 / 2.0.

### R5.2 in-flight primary sleep block
- magnitude ≥ 6 h trigger.
- destination-night overlap ≥ 3 h trigger.
- `block_target = 6 h`.
- `arrival_buffer = 60 min`.
- `creep = 90 min`.
- emergency nap upper bound `20–30 min`; required ≥ 6 h gap from main block.

### R5.3 meal anchors
- breakfast 07:30 / lunch 12:30 / dinner 19:30 destination local.
- magnitude ≥ 6 h trigger.
- 22:00–05:00 destination-local dead zone.
- 90 min dinner-before-sleep gap.
- Macro guidance (protein-forward breakfast / balanced lunch / light dinner) is UX copy only.

### R6 melatonin
- 0.5 mg default dose.
- 2–4 h DLMO-relative window edges (literature supports 2–4 h; we default to 3 h).
- 2–3 post-arrival taper nights.

### R7 caffeine
- `bedtime − 8 h` advance cutoff, `bedtime − 6 h` delay cutoff.
- `travel_day_cutoff = block_start − 8 h`.
- "Little and often beats a single slug" dose-pattern guidance.

### R8.1 sleep-bank
- exact sleep-bank formula `latest = wake − target_sleep_h`.
- 0.5× anti-reversal softening factor for D−2 drift.

### R8.2 / R5.2 in-flight threshold
- `flight_duration > 9 h` long-haul threshold.

### R9 wind-down
- 30 min hard minimum.
- 3 h full target.

### R10 wearable modulation
- HRV trend `< −10%`, RHR trend `> +5 bpm`, sleep score `< 30d_avg − 1 SD`.
- multipliers `× 0.5` pre-flight drift, `× 0.7` nap aggressiveness.
- manual fallback softening: good / mixed / poor → 0% / 25% / 50%.

### R11 short-trip gates
- `nights ≤ 2 → skip`.
- `nights == 3 and magnitude < 5 h → minimal`.
- `magnitude < 3 h and nights < 5 → minimal`.

### R12 age modifiers
- `age ≥ 55 → confidence × 0.8`.
- `+ 30 min` strong-seek extension for cataract/AMD or low-outdoor-light routine.

### R13 slow metabolizer
- `+ 2 h` caffeine cutoff extension.

---

## Calibration changelog

### v0.3.0 — 2026-05-13 (science-integrity pass)
- **R6 rewritten.** Phase-relative timing (`current_DLMO − 3 h ± 1 h`); explicit removal of `takeoff + 1 h` and `block_start − 15 min` anchors. Default dose narrowed to **0.5 mg oral immediate-release** (sublingual not supported under these timings). Math corrected: `DLMO − 3 h = CBT_min − 10 h` (not `− 10.5 h`); bedtime-anchored shortcut is intermediate-only. Sedating-medication / alcohol suppression added.
- **R8.1 fixed.** Direction-agnostic `min(protocol_bedtime, latest_for_target_sleep)`; the previous direction-branched `max()` on advance silently disabled sleep-banking on early-flight advance trips.
- **R8.3 rewritten.** Wake time is the *behavioral* anchor; light is the *circadian phase-shifting* anchor and remains PRC-gated. The previous "strong-seek light on wake, regardless of CBT_min" was removed — it silently violated the Khalsa PRC on early-adaptation days.
- **R12 rewritten.** Age no longer scales `rate_for_direction` (Kripke 2007 did not support a universal age effect on PRC amplitude). Age becomes a phase-estimate-confidence and light-delivery modifier with conditional outdoor-light preference and strong-seek window extension when cataract / AMD or low-outdoor-light routine is disclosed.
- **R5.2 added.** Recommended primary in-flight sleep block, direction-specific placement, quarantined constants, no melatonin coupling, no early homeostatic-clearance nap, emergency-nap escape hatch (≤ 20–30 min).
- **R5.3 added.** Destination-aligned meal anchors as a **peripheral / metabolic** timing cue (Wehrens 2017; Vetter 2017). Explicitly NOT a master-clock resetter. Macro guidance is UX copy, not a circadian claim.
- **R7 expanded.** Travel-day caffeine cutoff anchored to `in_flight_sleep_block.start − 8 h` when R5.2 fires; "No caffeine today" branch when cutoff < wake. Rationale copy switched to "residual stimulation," removing "peak inhibition exactly when you need to sleep." Post-landing partial-alertness caveat when sedating medication / alcohol disclosed.
- **R4 fixed.** "Dim-safe / evening-target" tier added at 1–10 melanopic EDI (previously a gap). DER conversion expressed as ranges, not fixed constants. Conservative fallback direction: upper-bound DER for evening avoidance, lower-bound DER for daytime seeking. R4.1 sensitivity multipliers quarantined.
- **R5.1 reworded.** Post-arrival behavior follows the destination schedule, but light **remains PRC-gated using the current phase estimate** — removes the conflict with R8.3.
- **R9 expanded.** 30 min hard minimum + 3 h full target (Brown 2022).
- **R10 quarantined.** Wearable thresholds and multipliers explicitly labelled engineering heuristics; Chinoy 2021 supports trend-vs-baseline framing only, not specific thresholds.
- **R11 rewritten.** Trip-length primary tiered logic (2 / 3 / 5 night gates), Arendt 2009.
- **R2 expanded.** "Evaluate both directions" guidance for advance ≥ 8 h shifts.
- **Parameter quarantine appendix.** Expanded to cover every heuristic constant in the file. Single annotation per entry: "engineering heuristic, not directly literature-derived."

### v0.2.0 — earlier 2026 (post-2015 literature integration)
- Melanopic EDI introduced via Brown et al. 2022.
- Phillips 2019 individual-variability framing.
- Roenneberg / MCTQ wake-anchor framing (since softened in v0.3.0).

### v0.1.0 — initial release
- R1–R13 baseline.
