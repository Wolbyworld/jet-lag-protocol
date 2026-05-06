# St Hilaire, Gooley, Khalsa, Kronauer, Czeisler, Lockley 2012

*Human phase response curve to a 1 h pulse of bright white light.* J Physiol 590(13):3035–3045. PMC3406389.

## Methods (one line)
PRC to a single 1 h bright light pulse (~10,000 lux), same constant-routine protocol as Khalsa 2003 but shorter exposure.

## Numbers used by the algorithm

- **Maximum delay (1 h pulse)**: ~−2.0 hours
- **Maximum advance (1 h pulse)**: ~+1.2 hours
- **Asymmetry**: delay capacity > advance capacity for short exposures (consistent with τ > 24 h asymmetry)
- **Duration-response (combined with prior literature)**:

| Pulse duration | Phase shift magnitude (delay) |
|---|---|
| 0.2 h | ~1.1 h |
| 1.0 h | ~1.6 h |
| 2.5 h | ~2.3 h |
| 4.0 h | ~2.7 h |
| 6.5 h | ~3.1 h |

  Approximately linear from 0.2 to 2.5 h, then saturating.

## Practical implications

- A 30–60 min outdoor light exposure produces ~70–80% of the maximum advance/delay achievable from an extended pulse.
- Algorithm's "30 min strong-seek minimum dose" is grounded here: short exposures are highly leveraged.
- Justifies the intermittent-exposure pattern (30 min on / 30 min off, used in Eastman protocols) as nearly-equivalent to continuous exposure.

## Algorithm dependence

R3 (rate-per-day calibration) and R4 (intensity tiers) reference this paper for realistic exposure-duration scaling. The algorithm assumes each prescribed strong-seek window of ≥30 min delivers approximately the magnitude listed in the duration-response table above.
