# 04 – O₂ Analyzer Auto-Calibration

**Platform:** Allen-Bradley MicroLogix 1100 · RSLogix 500 · RSEmulate 500 · *simulated*

## Overview
An O₂ analyzer in a coal-mine application drifts over time. The program has two cycles:
- **Sampling** – scale the raw signal to a 0–40 % O₂ reading.
- **Calibration** – open the 0 % reference gas for 30 s, then the 30 % reference gas for 30 s, average the readings from each period, and **retune the scaling limits** so the reading is correct again.

## Memory map
| Address | Description |
|---|---|
| `N7:0` | Raw sensor signal (0–16383) |
| `N7:1` | Measured O₂ concentration (%) |
| `N7:2` / `N7:3` | Input min / input max used by the scaling (`SCP`) instruction |
| `B3:0/0` | Calibrate button |
| `B3:0/3` | Sampling-cycle start/stop |
| `O:0/0` / `O:0/1` | 0 % gas valve / 30 % gas valve |
| `F8:4` / `F8:5` | Running sums of 0 % and 30 % samples |
| `C5:0` / `C5:1` | Sample counters for each period |
| `F8:6` / `F8:7` | Average reading at 0 % / at 30 % |
| `F8:0` | Ratio 40 / 30 (= 1.3333) |

## Program structure
| File | Routine | Role |
|---|---|---|
| 3 | `IO` | Valve bits to outputs |
| 4 | `CTRLCYC1` | Normal sampling and scaling (`SCP`, 0–40 %) |
| 5 | `CTRLCYC2` | Calibration sequence, sampling, averaging, new limits |
| 6 | `RECONFIG` | Clears accumulators and counters when a cycle starts |

## Calibration math
```
Input Min = Average_0%
Input Max = (40 / 30) × (Average_30% − Average_0%) + Average_0%
```
The routine takes one sample per second during each 30 s period, sums them, counts them, and divides sum by count. It then writes `N7:2` (min) and `N7:3` (max) and rescales the measurement with `SCP` using the new limits.

## Test procedure (from the project brief)
1. `N7:0` = 8192 → `N7:1` ≈ 20 %.
2. `N7:0` = 0, start calibration; change `N7:0` to 12288 exactly when the 30 % gas stage begins. After calibration, `N7:1` ≈ 30 %; `N7:0` = 0 → ≈ 0 %; `N7:0` = 16383 → ≈ 40 %.
3. Repeat with an offset sensor: 100 for the 0 % stage and 11000 for the 30 % stage. After calibration, 100 → ≈ 0 %, 14633 → ≈ 40 %.

## Concepts demonstrated
Sensor drift compensation, two-point calibration, timed valve sequencing, running averages, runtime retuning of scaling parameters, float arithmetic.
