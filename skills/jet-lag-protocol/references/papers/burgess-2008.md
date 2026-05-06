# Burgess, Revell, Eastman 2008

*A three pulse phase response curve to three milligrams of melatonin in humans.* J Physiol 586(2):639–647. PMC2375577.

## Methods (one line)
3-pulse PRC to 3.0 mg oral melatonin: each session ran 7–9 days fixed sleep + 5-day lab session with 3 daily melatonin pulses in an ultradian dim/dark cycle (light:dark 2.5:1.5 h, <150 lux).

## Numbers used by the algorithm

- **Maximum advance**: ~+1.8 h, when 3.0 mg is administered ~6 h before DLMO (i.e. early afternoon body-time)
- **Maximum delay**: ~−1.3 h, when administered ~5 h after DLMO (i.e. early morning body-time, near wake)
- **Crossover**: ~3 h after DLMO (early biological night)

## Practical implications

- The 3.0 mg PRC is the original reference but is **not the dose used by the algorithm by default**. R6 specifies 0.5 mg because:
  - The 3.0 mg dose produces elevated daytime melatonin → grogginess
  - The 0.5 mg dose produces equivalent shifts when timed correctly (Burgess 2010)
  - The 0.5 mg dose has shorter duration of action (less spillover into next day)
- This paper is included for completeness because some users self-medicate at 3.0+ mg (most over-the-counter melatonin in the US is 3–10 mg). When the user reports a dose ≠ 0.5 mg, the algorithm should reference this PRC for the larger doses and shift the optimal dosing time accordingly.

## Dose-time relationship vs 0.5 mg

Compared to the 0.5 mg PRC (Burgess 2010), the 3.0 mg PRC peaks **~1.5 h earlier** in the body's day. So:
- Optimal advance dose time, 3.0 mg: ~6 h before DLMO
- Optimal advance dose time, 0.5 mg: ~3 h before DLMO

## Caveats

- 3-pulse paradigm doesn't directly translate to single-dose effects; magnitudes for one pulse alone are smaller (~⅓ of three-pulse magnitude).
- High-dose melatonin can cause vivid dreams, morning grogginess, transient hypotension; user-reported tolerance varies.

## Algorithm dependence

R6 (melatonin dose timing) primarily uses Burgess 2010 (0.5 mg PRC). This paper is the reference for higher-dose users and is loaded only when the user reports a non-default dose.
