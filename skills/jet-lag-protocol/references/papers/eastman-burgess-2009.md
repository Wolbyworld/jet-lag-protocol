# Eastman, Burgess 2009

*How to travel the world without jet lag.* Sleep Med Clin 4(2):241–255. PMC2829880.

## Methods (one line)
Synthesis review of pre-flight, in-flight, and post-arrival circadian protocols for advance and delay shifts. Translates PRC literature (Khalsa, Burgess, Czeisler) into operational recommendations.

## Numbers used by the algorithm

### Adaptation rates with full intervention

| Direction | Intervention | Rate |
|---|---|---|
| Advance (eastward) | Light + melatonin + sleep shift | ~1.0 h/day |
| Delay (westward) | Light + melatonin + sleep shift | ~1.5 h/day |

### Adaptation rates without intervention (real-world flights)

| Direction | Rate |
|---|---|
| Advance | ~57 min/day |
| Delay | ~92 min/day |

### Rationale for asymmetry
Human free-running period τ ≈ 24.2 h (slightly long). The SCN drifts later naturally → delays are with the natural drift, advances against it.

### Operational rules

- **Pre-flight days**: 2 days for both directions is typical. 3 days is feasible for advances ≥6 h.
- **Per-day shift**: ~1 h/day of behavioral sleep schedule shift, paired with light protocol that aligns to the new schedule.
- **In-flight strategy**:
  - Advance: sleep aligned to destination night
  - Delay: stay awake into destination evening; brief sleep only in late flight
- **Post-arrival**: anchor wake time to destination clock.

### Direction-decision rule
- If shift > 12 h, going around the long way (opposite direction) is faster.
- For very short trips (<3 days at destination, <3 h shift), don't shift at all — stay on home time.

## Practical implications encoded in algorithm

- R3 (adaptation rates) uses these numbers directly.
- R8.1 (pre-flight bedtime drift) uses 1 h/day as the protocol rate, modulated by the sleep-bank rule for early-departure logistics.
- R11 (trip-length minimums) uses the <3 days / <3 h thresholds.
- R2 (12-hour rule) is justified here.

## Caveats

- This is a review, not a primary trial. The rates cited are best-case scenarios from the underlying literature; field results vary.
- "Full intervention" assumes user adherence to all of: light protocol, melatonin protocol, sleep schedule shift. Partial adherence yields proportionally smaller shifts.
- The advance rate (1 h/day) is hard to exceed without inducing significant circadian misalignment; pushing 1.5 h/day advance is documented in lab settings but produces sleep debt.

## Algorithm dependence

R2, R3, R8, R11 reference this paper. It's the operational backbone of the algorithm — the PRC papers tell us *what* shifts; this paper tells us *how much per day* and *with what schedule*.
