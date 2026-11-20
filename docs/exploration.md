# Preliminary exploration

Status: preliminary. These notes describe the exploratory work done before this
repository was started. The exploratory scripts are not part of the repository, and
none of their results are reported as project results. This repository's code will
reproduce what is kept.

## Purpose

Check, before building anything, that real data exists and is usable for hourly PM2.5
forecasting in Senegal.

## Method

1. Listed the OpenAQ v3 stations for Senegal and the PM2.5 sensors they expose.
2. Kept stations with recent measurements, then computed daily completeness and the
   longest gaps.
3. For the retained stations, downloaded hourly means and measured completeness,
   gaps, exact zeros, stuck values and spikes above 500 µg/m³.
4. Re-checked the last 14 days of availability on 7 November 2026.
5. Ran a first gradient-boosting model against a persistence baseline.

## Findings

- 38 OpenAQ stations in Senegal, all AirGradient low-cost sensors; 26 active with PM2.5;
  PM10 almost absent.
- 7 stations retained after the hourly completeness check, with about one year of
  hourly history (from November or December 2025).
- Cleaning rules adopted: spikes above 500 µg/m³ masked, exact zeros treated as
  missing, gaps of up to 3 hours filled.
- One station showed 80 to 100 % exact zeros from May to August, while another showed
  none: this looks like a sensor fault, not a real measurement.
- After cleaning, availability ranged from 67 to 97 % depending on the station.
- On 7 November 2026, 6 of the 7 stations had full availability over the previous
  14 days; one had only 8 %.
- A first model beat persistence at 1, 3 and 6 hours, but it detected pollution
  episodes poorly and did not help on one station. These results are not reproduced
  in this repository yet, so no figures are given here.

## Open points

- Station coordinates must be checked before stating anything about geographic coverage.
- OpenAQ data licences must be verified before any redistribution.
- No comparison with a reference instrument has been done.