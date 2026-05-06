# Burgess 2014

*The dim light melatonin onset following fixed and free sleep schedules.* J Sleep Res 23(5):516–522. PMC3841975.

## Methods (one line)
Healthy adults assessed for DLMO under fixed-sleep schedules (constrained to a target bedtime/wake time) and free-sleep schedules (ad-libitum). Compared regression equations for predicting DLMO from sleep-time variables.

## Numbers used by the algorithm

### Population-mean phase angles (free-sleep, healthy adults)

- **DLMO occurs ~2.5 h before habitual bedtime** (sleep onset)
- **DLMO occurs ~13.5 h after habitual wake time**

Both estimators converge for users sleeping ~7–8 h.

### Regression equation (free-sleep)

```
DLMO (decimal hours) = 0.80 × wake_time_decimal − 8.83
```

Where decimal hours treat midnight as 0 and use 24-hour clock; result in negative numbers represents "h before midnight" (e.g. −4.0 = 20:00).

This is the most accurate single-variable predictor of DLMO from self-report.

### Wake-time vs bedtime correlation with DLMO

Wake time correlates more strongly with DLMO than bedtime does. The skill's R1 uses bedtime − 2.5 h as the primary estimator (more familiar to users) and wake time + 13.5 h as the cross-check.

## Practical implications encoded in algorithm

- R1's chronotype offsets (early=2.0 h, intermediate=2.5 h, late=3.0 h) are calibrated against this paper's population mean of 2.5 h.
- When the two estimators disagree by >2 h, the algorithm flags it for the user — the disagreement usually means short or long sleep duration, or a mismatch between reported bedtime and actual sleep onset latency.

## Caveats

- Population means; individual variation is ~±1 h.
- Free-sleepers may differ phase from the same person under fixed schedules (forced wake-up shifts the phase relationship).
- Older adults phase-advance ~30 min relative to younger adults; the regression doesn't include age as a covariate (Kantermann 2015 does).

## Algorithm dependence

R1 (phase markers from self-report) is calibrated against this paper. The 2.5 h offset and 13.5 h cross-check are population-mean values from this regression analysis.
