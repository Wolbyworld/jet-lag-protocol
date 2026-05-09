# Chinoy, Cuellar, Huwa, Jameson, Watson, Bessman, Hirsch, Cooper, Drake, Markwald 2021

*Performance of seven consumer sleep-tracking devices compared with polysomnography.* Sleep 44(5):zsaa291. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC8121290/))

**The reference for "is wearable data trustworthy enough to drive an algorithmic decision?" Directly grounds R10 (sleep-quality modulation).**

## Methods (one line)
Validation study comparing 7 consumer sleep trackers (Fatigue Science Readiband, Fitbit Alta HR, Fitbit Inspire HR, Garmin Fenix 5S, Garmin Vivosmart 4, Withings Aura Day, Oura Ring) to gold-standard polysomnography (PSG) in healthy adults across 2 in-lab nights each.

## Numbers used by the algorithm

### Total sleep time accuracy

Most devices within ±15 to ±30 min of PSG-derived total sleep time. Best performers: Oura, Garmin Fenix. Acceptable for **trend tracking** within an individual; not for absolute thresholds.

### Sleep stage accuracy

| Stage | Best device agreement | Worst device agreement |
|---|---|---|
| Wake | ~85% epoch-by-epoch | ~70% |
| Light | ~70% | ~50% |
| Deep | ~55% | ~30% |
| REM | ~65% | ~40% |

**Sleep stage data from consumer devices is substantially error-prone.** Do not gate algorithm decisions on absolute deep-sleep or REM-sleep amounts. Use total sleep time and continuity proxies only.

### Sleep efficiency / "score" reliability

Vendor-specific composite scores (Garmin sleep score, Oura sleep score, Whoop sleep performance) are useful for **trend within an individual** (compare to your own 14-day baseline) but not as absolute thresholds (a "70" on Oura is not equivalent to "70" on Garmin).

### HRV measurements

- Resting HRV (RMSSD) on most devices: ±5–10 ms of ECG-derived gold standard. Acceptable for trend.
- Acute HRV during awake periods: substantially less reliable across all consumer devices

## Practical implications encoded in algorithm

### R10 thresholds should be relative, not absolute

The current R10 sleep-quality modulation rule (in `algorithm-rules.md`):
> *"if 7d_sleep_score < 70 OR HRV_trend < −10% baseline: pre_flight_drift_rate *= 0.5"*

The **HRV_trend < −10% baseline** part is correct (relative to personal baseline) ✓.
The **7d_sleep_score < 70** part is wrong — that's an absolute threshold that doesn't translate across vendors. Should be:
> *"if 7d_sleep_score < (your_personal_30d_average − 1 SD): soften..."*

### Wearable input contract should specify trend, not absolute

When the algorithm consumes wearable data, it should request:
- **Sleep duration last 7 days**: absolute value, OK
- **Sleep efficiency / score trend**: signed delta from personal 30-day baseline, NOT absolute
- **HRV trend**: signed delta from personal baseline (rMSSD), NOT absolute
- **RHR trend**: signed delta, NOT absolute

This is what the Chinoy paper supports: device-relative trends are reliable; cross-device or absolute thresholds are not.

### Don't use sleep-stage data

Several wearables expose deep/REM sleep. **The algorithm should not use these.** Per Chinoy 2021, accuracy is too poor to drive decisions. R10 should explicitly avoid stage-based modulation.

## Caveats

- Short-duration validation (2 in-lab nights per device); long-term real-world performance may differ
- Healthy adults only; clinical sleep populations may show different device errors
- Devices are generation-of-test (2018–2020 firmware); newer firmware may improve. Re-validate if a vendor claims a major sleep-tracking update.
- Apple Watch wasn't included in this 2021 cohort; later validation studies (e.g. Roberts 2022) suggest similar trend-OK / absolute-not-OK pattern

## Algorithm dependence

- **R10 (sleep-quality modulation)** — change all absolute thresholds to relative-to-personal-baseline
- **R10 inputs schema** — explicitly request "trend vs baseline" not "absolute value"
- **Avoid sleep-stage gating** — R10 should not depend on deep-sleep or REM-sleep absolute durations from wearables
- **Vendor-agnostic** — algorithm should not require a specific brand; any device producing the abstract signals (sleep score trend, HRV trend, RHR trend) suffices
