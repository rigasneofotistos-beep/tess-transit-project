# WASP-18 b with TESS: from raw light curve to transit parameters

I measured the orbit and size of the hot Jupiter WASP-18 b from public NASA TESS data (Sector 2, 2-min cadence) and compared my results with published analyses. The aim was to build and check a full analysis pipeline on a known planet before applying it to harder targets.

![Transit fit](figures/transit_fit.png)

## Method
1. Downloaded the SPOC light curve from MAST with `lightkurve`.
2. Removed slow trends with a Savitzky–Golay filter, masking the transits so the filter would not distort them.
3. Found the period with a Box Least Squares periodogram.
4. Measured the time of each of 27 complete transits and fitted a straight line to get the period (O − C diagram below).
5. Wrote my own transit model with quadratic limb darkening (fixed to Claret 2017 values) and a free baseline.
6. Estimated uncertainties with MCMC (`emcee`, 24 walkers × 1,500 steps).

## Results

| Parameter | This work (Sectors 2+3) | Deline et al. (2025) | Shporer et al. (2019) |
|---|---|---|---|
| Period P [d] | 0.9414599 ± 0.0000032 | — | 0.9414526 ± 0.0000016 |
| Rp/Rs | 0.09752 ± 0.00014 | 0.09757 ± 0.00013 | 0.09716 ± 0.00014 |
| a/Rs | 3.483 ± 0.022 | 3.493 ± 0.011 | 3.562 ± 0.022 |
| Impact parameter b | 0.359 ± 0.017 | 0.3605 ± 0.009 | 0.318 ± 0.019 |

My results agree within 0.5σ with Deline et al. (2025), the most complete analysis to date (5 TESS sectors, CHEOPS, free limb darkening). With one sector, a/Rs and b differed from Shporer et al. (2019) by about 3σ; adding Sector 3 reduced this to 2.5σ. Deline et al. report a similar offset from Shporer et al., so the earlier tension is not specific to my method.

![O − C](figures/o_minus_c.png)
![Corner plot](figures/corner.png)

## What I learned from the errors
- **Detrending can distort transits.** Without a transit mask, four transits near data gaps came out too shallow.
- **The baseline matters.** Fixing the out-of-transit flux at exactly 1 made the planet 1.7% too large. A free baseline (−332 ppm) brought Rp/Rs in line with the literature.
- **A search method is not a measurement method.** The BLS period was limited by its grid; individual transit times gave a period good to 0.3 s.

## Limitations and next steps
- Sectors 2 and 3 only; my uncertainties are probably slightly underestimated (fixed limb darkening, no correlated-noise model).
- Next: add Sector 3 to test the a/Rs–b tension, then apply the pipeline to a smaller planet.

## Tools
Python 3.12 · lightkurve · numpy · scipy · emcee · corner · matplotlib