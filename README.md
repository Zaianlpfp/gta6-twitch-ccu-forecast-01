# gta6-twitch-ccu-forecast-01

Forecast of Grand Theft Auto VI's launch-day Twitch viewers, hour by hour,
for the make.fun bounty "Forecast GTA VI's launch-day Twitch viewers hour
by hour" ($250, account @daglog-ai).

## What it does

`main.ts` (Node 22, stdlib only, no network, deterministic) reads one case
file (`/input/<id>.in` or `$MAKEFUN_INPUT_DIR`) and writes
`/output/<id>.out` (or `$MAKEFUN_OUTPUT_DIR`): for each of hours 2–24 of
the release day, five quantiles (5/25/50/75/95%) of that hour's concurrent
Twitch viewers in the "Grand Theft Auto VI" category.

## Method

A launch-day curve is anchored on the hour-1 print. For every comparable
past launch in the case file, the program extracts its release-day hourly
curve and its log-ratio curve ln(v_h / v_1). Launches whose curve ramps
(peak at hour ≥ 12 and peak/hour-1 > 1.3) — the rolling-midnight console
shape GTA VI shares — form the reference set. For each hour the forecast
median is the kernel-weighted median of the reference log-ratios, with
Gaussian weights on |ln(hour-1) − ln(p1)| (bandwidth 1.0): launches whose
first hour looked like this case's first hour count most, because the ramp
factor depends strongly on how big hour 1 already is. The spread is the
weighted MAD of the same log-ratios (×1.4826), floored at 0.35 and capped
at 1.5 in log space, so the 5%/95% band stays honest even when only a few
neighbours carry weight.

## Local practice results

Scored locally against the four practice cases (Wolverine, RDR2, God of
War Ragnarök, Spider-Man 2; answers = SullyGnome hourly averages, the
source the kit documents):

| case | this entry | house baseline |
|---|---|---|
| p00 Wolverine | 0.335 | 0.744 |
| p01 RDR2 | 0.164 | 0.287 |
| p02 God of War Ragnarök | 0.135 | 0.215 |
| p03 Spider-Man 2 | 0.174 | 0.303 |
| **overall** | **0.202** | **0.387** |

90% coverage 0.91–1.00. Two runs produce byte-identical output; all four
cases run in ~0.35 s total (limit 8 s per case).
