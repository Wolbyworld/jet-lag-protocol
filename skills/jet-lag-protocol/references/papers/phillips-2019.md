# Phillips, Vidafar, Burns, McGlashan, Anderson, Rajaratnam, Lockley, Cain 2019

*High sensitivity and interindividual variability in the response of the human circadian system to evening light.* PNAS 116(24):12019–12024. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC6575863/))

**Updates Zeitzer 2000's population-mean dose-response with two findings: lower threshold than previously reported, and dramatic individual variability.**

## Methods (one line)
55 healthy adults (18–30) exposed to dim control (<1 lux) and 5 evening light intensities (10, 30, 50, 100, 200, 400, 1000, 2000 lux) for 5 h on separate study nights, with melatonin suppression measured per intensity per individual.

## Numbers used by the algorithm

### Population-mean dose-response

- **ED50 (50% melatonin suppression) = 24.6 lux** (photopic) for evening exposure
- ED50 in **lux melanopic EDI ≈ 14** lux (using broadband 5000K light)
- For comparison: Zeitzer 2000 ED50 ≈ 120 lux

The threshold for circadian non-image-forming response is **~5× lower than the older literature**. Many environments previously considered "dim" (e.g. soft warm lighting at 30–50 lux) substantially suppress melatonin in average adults.

### Individual variability

- **ED50 ranges from 6 lux (most sensitive individual) to 350 lux (least sensitive individual)**
- Coefficient of variation: 26%
- Range > 50× across the cohort

The same evening lighting environment produces:
- ~60% melatonin suppression in the most sensitive 25%
- ~10% melatonin suppression in the least sensitive 25%

This is **the field's strongest evidence that population-mean prescriptions miss substantially at the tails.**

### Predictors of light sensitivity

The paper does not identify reliable demographic or genetic predictors. Stone, McGlashan, Quin, Skinner, Stephenson, Cain, Phillips 2020 (*JBR*) follow-up suggests intrinsic period (τ) and pupil size correlate weakly but neither explains the bulk of variance. **In practice: light sensitivity is currently unmeasurable without a direct dose-response test.**

## Practical implications encoded in algorithm

- **R4 strong-seek lower bound should drop**. The current algorithm's "strong-seek = ≥5,000 lux" is far above what's needed for entrainment in average users. A 250 melanopic EDI sustained dose (Brown 2022) is sufficient.

- **R4 soft-avoid upper bound should drop**. The current "soft-avoid 10–100 lux" includes intensities (50–100 lux) that meaningfully suppress melatonin in sensitive users. Tighten to 10–30 melanopic EDI.

- **A "light sensitivity" personalization knob is justified.** Three-bucket approximation:
  - **Sensitive** (~25% of population, ED50 < 15 lux): tighter evening dim, weaker strong-seek doses also effective
  - **Average** (~50% of population, ED50 ~25 lux): current population-mean prescriptions OK
  - **Resistant** (~25% of population, ED50 > 60 lux): need substantially higher strong-seek doses; less benefit from tight evening dim

- **Or: feedback-driven dose calibration.** Start at the population-mean dose; if user reports no shift after 2 days, escalate; if user reports vivid dreams or insomnia, soften.

## Caveats

- Cohort is young adults (18–30). Older adults likely have different baseline ED50 (lens yellowing reduces blue-light transmission ~50% by age 60). The algorithm should consider age-scaling.
- Single 5-h evening exposure protocol; multi-night cumulative effects not directly tested
- Self-selected healthy population — clinical populations (mood, sleep, neurological disorders) may have shifted distributions

## Algorithm dependence

- **R4 (light intensity tiers)** — narrow the soft-avoid band, drop strong-seek floor
- **New rule R10.2: light sensitivity modulation.** If user reports a 3-bucket sensitivity (or has wearable-derived melatonin proxy), scale strong-seek required dose by ~0.5x (sensitive) / 1.0x (average) / 2.0x (resistant)
- **R12 (older travelers)** — citation strengthened. Lens yellowing implies older users probably need 1.5–2x the dose for same circadian effect, beyond the PRC amplitude shrinkage already noted
