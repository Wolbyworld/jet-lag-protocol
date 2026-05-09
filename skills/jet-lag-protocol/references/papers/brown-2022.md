# Brown, Brainard, Cajochen, Czeisler, Hanifin, Lockley, Lucas, Münch, O'Hagan, Peirson, Price, Roenneberg, Schlangen, Skene, Spitschan, Vetter, Zee, Wright 2022

*Recommendations for daytime, evening, and nighttime indoor light exposure to best support physiology, sleep, and wakefulness in healthy adults.* PLOS Biology 20(3):e3001571. ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC8929548/))

**Replaces Zeitzer 2000 as the canonical light prescription for the field. Brings melanopic EDI metrology into operational guidance.**

## Methods (one line)
Expert consensus statement from a 23-author international panel synthesizing 2 decades of laboratory data on long-duration light exposure (>2 h) effects on melatonin suppression, circadian phase resetting, alerting responses, and sleep, expressed in **melanopic equivalent daylight illuminance (melanopic EDI)** per CIE S 026:2018.

## Why melanopic EDI, not lux

The intrinsically photosensitive retinal ganglion cells (ipRGCs) — the primary mediators of non-image-forming circadian effects of light — are maximally sensitive at ~480 nm (cyan). Standard photopic lux is weighted to V(λ), the photopic luminosity function peaked at 555 nm (green) — not to melanopsin. **For the same photopic lux value, light spectrum dramatically changes circadian impact:**

- Standard incandescent (2700 K): melanopic EDI ≈ 0.45 × photopic lux
- Daylight LED (5000 K): melanopic EDI ≈ 0.9 × photopic lux
- Daylight (≥6000 K, blue sky): melanopic EDI ≈ 1.1 × photopic lux

A 200-lux warm-LED bedside lamp has a ~90 melanopic-EDI footprint; a 200-lux daylight LED has ~180. Same lux meter reading, twice the circadian impact.

## Numbers used by the algorithm

| Period | Recommended threshold | Rationale |
|---|---|---|
| **Daytime** | melanopic EDI **≥ 250 lux** | Promotes alertness, supports day-time circadian alignment |
| **Evening (3 h pre-bed)** | melanopic EDI **< 10 lux** | Below threshold for melatonin suppression in most adults |
| **Sleep environment** | melanopic EDI **< 1 lux** | Maintains nocturnal melatonin profile |

For comparison to Zeitzer 2000:
- Zeitzer's half-saturation: ~120 lux photopic (~50–110 melanopic depending on source)
- Brown's evening threshold: < 10 melanopic EDI
- These are not directly comparable but indicate a substantial tightening of recommended evening exposure ceilings

## Practical implications encoded in algorithm

- **R4 (light intensity tiers) should migrate from photopic lux to melanopic EDI.** New tier definitions:

  | Tier | Melanopic EDI | Practical |
  |---|---|---|
  | Strong-seek | ≥ 250 | Direct outdoor sun/sky, >5,000 photopic lux from broadband source |
  | Mild-seek | 50–250 | Bright indoor near window, well-lit office at 5000K |
  | Soft-avoid | 10–50 | Dim indoor, sunglasses outdoors, "warm white" lamp at moderate intensity |
  | Full-avoid | < 1 | Eye mask, blackout, eyes closed |

- **Evening prescription tightened.** Brown's "< 10 melanopic EDI 3 h pre-bed" is more aggressive than typical "dim screens" advice. Practical: night-shift screens (true 1900K warm filter), dimmed phone (<10% brightness), no kitchen overhead light.

- **Reading lamps differ.** The algorithm should distinguish "warm dim lamp" (low melanopic, low circadian impact, OK in evening) from "task lighting LED at the desk" (high melanopic, high circadian impact, avoid in evening) — currently it doesn't.

## Caveats

- Recommendations are for **healthy adults**; clinical populations not addressed
- Long-duration exposures (>2 h) — short-pulse effects (e.g., 30-min bright-light therapy) operate on a different dose-response surface
- Inter-individual variability is acknowledged but not parameterized in the recommendations themselves (see Phillips 2019 for that)
- Daytime ≥ 250 melanopic EDI is **higher than typical office lighting** (most offices are ~100–200 photopic lux warm = ~50–100 melanopic EDI). This creates a real intervention gap for desk-bound workers.

## Algorithm dependence

- **R4 (light intensity tiers)** — replace Zeitzer 2000-derived photopic lux thresholds with Brown 2022 melanopic EDI thresholds
- **R8.3 (wake-anchor light rule)** — quantify "strong-seek light" as ≥ 250 melanopic EDI sustained for ≥30 min
- **Recovery-day light prescription** — same standard applies; current "bright outdoor light within 30 min" should be specified as ≥ 250 melanopic EDI to be operationally precise
