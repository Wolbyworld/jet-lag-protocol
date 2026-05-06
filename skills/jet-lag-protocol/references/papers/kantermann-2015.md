# Kantermann, Sung, Burgess 2015

*Comparing the Morningness-Eveningness Questionnaire and Munich ChronoType Questionnaire to the Dim Light Melatonin Onset.* J Biol Rhythms 30(5):449–453. PubMed 26243627.

## Methods (one line)
Healthy adults completed both MEQ and MCTQ; DLMO measured separately. Multivariate regression to identify which questionnaire score best predicts DLMO.

## Numbers used by the algorithm

### Single-variable correlations with DLMO

| Predictor | Correlation r |
|---|---|
| MEQ score | −0.70 |
| MSFsc (MCTQ-derived mid-sleep on free days, sleep-corrected) | +0.68 |

### Multivariate regression
- MSFsc + age explain ~60% of DLMO variance
- MEQ + age explain ~50% of DLMO variance
- MSFsc is a slightly better predictor than MEQ; both are acceptable

### Why MSFsc beats MEQ
MEQ captures *psychological preference* (when you'd like to be awake/asleep). MSFsc captures *behavioral reality* (when you actually sleep when nothing forces you). For predicting circadian phase, behavior beats preference.

## Practical implications encoded in algorithm

- R1's chronotype-to-DLMO-offset table:
  - Early bird (MSFsc < 03:00, or MEQ > 60): DLMO_offset = 2.0 h before bedtime
  - Intermediate (MSFsc 03:00–05:00, or MEQ 42–58): 2.5 h
  - Late bird (MSFsc > 05:00, or MEQ < 42): 3.0 h
- These offsets are not from this paper's regression directly — they're a clinical translation. The paper validates that chronotype questionnaires *can* substitute for direct DLMO measurement, with ~60% variance accounted for.
- The skill prefers MSFsc when available (MCTQ takes 1 minute to complete); falls back to MEQ if user reports it.
- For users who don't want to take a questionnaire: ask "When do you naturally wake up on a free day?" and use that to compute MSFsc (mid-sleep on free days = (sleep_onset + wake_up) / 2).

## Caveats

- Both questionnaires were validated on healthy adults; clinical populations (DSPS, ASPS, shift workers) may not fit the regression.
- 60% variance accounted for means 40% unaccounted — individual DLMO can be ±1.5 h from the questionnaire estimate.
- Age matters: older adults phase-advance, the offset table doesn't account for this. Consider tightening offsets by 30 min for users >55.

## Algorithm dependence

R1 (phase markers) uses this paper to justify chronotype-questionnaire substitution for DLMO. The chronotype-offset table is calibrated to MEQ and MSFsc thresholds.
