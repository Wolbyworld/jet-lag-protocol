# Borbély 1982

*A two process model of sleep regulation.* Hum Neurobiol 1(3):195–204.

The foundational model of sleep regulation. Predates much of the circadian-shift literature but remains the canonical framework.

## The two processes

### Process S — homeostatic sleep pressure
- Builds during wake, decays during sleep
- Approximately exponential rise: S(t) ≈ Smax × (1 − e^(−t/τs))
- τs ≈ 18.2 h during wake (subjective)
- Sleep pressure subjectively perceived as fatigue/sleepiness

### Process C — circadian propensity to be awake
- Independent of how long you've been awake
- Peaks of alertness 2–4 h before habitual bedtime (the "wake-maintenance zone" or WMZ)
- Trough of alertness around CBT_min (early biological morning)
- Zero-crossing of circadian alertness ≈ sleep gate opens (typically 1–2 h after WMZ peak)

### Sleep timing emerges from S × C interaction
- You fall asleep when (Process S − Process C alertness) crosses a threshold
- If S is low and C is high (= WMZ), you cannot fall asleep — even with eyes closed
- If S is high and C is low (= late biological night), you cannot stay awake

## Practical implications encoded in algorithm

### Sleep-bank rule (R8.1)
The rule "the night before an early-departure flight, choose bedtime to ensure 7+ h of sleep before wake" depends on Process C:
- If you target a bedtime that falls inside your WMZ, you will lie awake (canonical traveler insomnia)
- WMZ is roughly bedtime[habitual] − 2 h to bedtime[habitual] − 4 h
- For early-bird user with bedtime 22:30: WMZ ≈ 18:30–20:30; sleep gate opens ~21:00
- Therefore: earliest viable sleep-bank bedtime is ~21:00 for this user

The algorithm should not propose a bedtime that lands inside the user's projected WMZ. If logistics force it, prescribe a strategic nap earlier in the day to dump Process S, then a "sleep gate" bedtime that respects the WMZ.

### In-flight nap rule (R8.2)
A short nap (<2 h) reduces Process S without erasing it. Long sleep (>4 h) erases enough Process S that destination bedtime becomes harder to fall asleep at. This is why the algorithm differentiates "nap block" (short, early in flight, allowed for advances) from "main sleep block" (long, late in flight, aligned to destination night).

### Wake-time anchoring (R8.3)
Process C is entrained primarily by light at wake (the "phase reset" of the SCN happens in early morning hours). Anchoring wake time to destination clock is the dominant entrainment cue; bedtime is lagged consequence.

## Caveats

- The model is descriptive, not perfectly predictive — individual variation in S build-up rate, WMZ timing, and sleep-gate sensitivity is large.
- Process S decay during sleep depends on sleep stage; not all sleep is equally restorative for the next day's S accumulation.
- Newer extensions (e.g. Phillips et al. 2010, three-process model with melatonin) refine the predictions but don't change the high-level rules.

## Algorithm dependence

R8 (sleep schedule) is fundamentally built on this model. The wake-maintenance zone constraint, the two-block in-flight sleep pattern, and the wake-time-anchoring rule for post-arrival adaptation are all consequences of the S × C interaction described here.
