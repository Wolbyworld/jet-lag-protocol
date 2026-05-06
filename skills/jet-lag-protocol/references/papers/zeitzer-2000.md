# Zeitzer, Dijk, Kronauer, Brown, Czeisler 2000

*Sensitivity of the human circadian pacemaker to nocturnal light: melatonin phase resetting and suppression.* J Physiol 526(3):695–702. PMC2270041.

## Methods (one line)
Healthy subjects exposed to single 6.5 h evening light pulses at varied intensities (3, 12, 36, 106, 1,260, 9,500 lux). Outcomes: phase shift of melatonin rhythm and acute melatonin suppression.

## Numbers used by the algorithm

The dose-response curve fits a logistic sigmoid on log-lux:

- **Threshold**: ~2 lux begins to produce a measurable effect
- **Half-saturation (phase shift)**: ~120 lux
- **Saturation (phase shift)**: ~550 lux
- **Saturation (melatonin suppression)**: ~200 lux

Specific intensity points measured:

| Intensity (lux) | Phase delay (h) | Melatonin suppression |
|---|---|---|
| ~3 | 0.07 | 11% |
| ~106 | 1.8 | 88% |
| ~1,260 | 2.6 | ~95% |
| ~9,500 | 3.2 | ~99% |

Half of the maximum phase-delay achievable with ~9,500 lux is reached by just over 1% of that intensity (~106 lux).

## Practical implications

- **The four-tier light model in R4 is a discretization of this sigmoid:**
  - Strong-seek (≥5,000 lux): fully saturated
  - Mild-seek (200–1,000 lux): half- to mostly-saturated; ~50–80% of strong-seek effect
  - Soft-avoid (10–100 lux): below half-saturation but above threshold; sub-effective for full shift, mildly counter-productive at the wrong time
  - Full-avoid (<2 lux): below threshold; functional zero
- **Implication for indoor work**: typical office lighting (300–500 lux) is in the half-saturation range, so the user is being phase-shifted whether they want to or not. Sunglasses indoors reduce eye-level lux to soft-avoid range.
- **Implication for screens**: phone/laptop screens at typical brightness deliver 30–80 lux at the eye — within soft-avoid range but non-trivial during biological night.

## Caveats

- Curve was fit for *evening* delay shifts; the exact saturation points for morning advance shifts may differ slightly (Khalsa 2003 used much higher intensities).
- Subjects were dark-adapted before the pulse (kept in 15 lux ambient daytime light prior); subjects with brighter daytime exposure history have a higher threshold.
- Inter-individual variation in light sensitivity is large (~3-fold range); the algorithm uses population means.

## Algorithm dependence

R4 (light tier definitions) is directly derived from this dose-response curve. The cutoffs are chosen at clinically meaningful sigmoid inflection points (threshold, half-saturation, saturation).
