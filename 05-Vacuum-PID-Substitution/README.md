# 05 – Vacuum Control Without a PID (Tiered Step Control)

**Platform:** Allen-Bradley MicroLogix 1100 · RSLogix 500 · RSEmulate 500 · *simulated*

## Overview
A vacuum pump on a receiver must hold a setpoint of 15 inHg, but a PID instruction is not allowed. The program evaluates the error every 10 s and nudges the pump speed up or down in steps that depend on how far off the vacuum is.

## Memory map
| Address | Description |
|---|---|
| `N7:0` | Vacuum sensor input (0–16383) |
| `N7:1` | Pump speed output |
| `N7:2` | Setpoint (15 inHg) |
| `N7:3` | Measured vacuum (0–30 inHg, scaled with `SCP`) |
| `F8:3` | Error = measured − setpoint |
| `F8:0` / `F8:1` / `F8:2` | Band edges: 4 %, 15 %, 30 % of the setpoint |
| `B3:0/0`, `/2` | Cycle start / stop buttons |
| `B3:0/6` | Cycle running |
| `T4:0` | 10 s evaluation timer |

## Program structure
| File | Routine | Role |
|---|---|---|
| 3 | `IO` | Start/stop one-shots, `SCP` scaling, pump-on bit |
| 4 | `CONTROLS` | Cycle latch, 10 s evaluation timer |
| 5 | `CONDITIONS` | Dead-band check, tiered adjustments |
| 6 | `CONFIG` | Setpoint, band edges (setpoint × 0.04 / 0.15 / 0.30), error calculation |

## Control rules
| |Error| relative to setpoint | Action every 10 s |
|---|---|
| within 4 % | no change |
| 4 % – 15 % | ±20 |
| 15 % – 30 % | ±60 |
| above 30 % | ±100 |

A **lower inHg value means more vacuum**, so a positive error (measured above setpoint, not enough vacuum) *adds* to the pump speed, and a negative error *subtracts* from it. For the negative side the error is sign-flipped (`NEG`) so the same band comparisons apply.

## Test procedure (from the project brief)
`N7:0` = 8192 and `N7:2` = 15 → measured ≈ 15. Then:
- 8750 → +20 every 10 s; 7650 → −20 every 10 s
- 10000 → +60; 6200 → −60
- 13000 → +100; 4000 → −100

## Concepts demonstrated
Custom control logic where PID is unavailable, dead-band, tiered gain scheduling, direction handling, `LIM` / `GRT` / `LEQ` compare instructions, configurable thresholds.

## Notes
Band sizes and the 10 s interval are test values; the logic carries over to a real system by changing the constants, which are held in the `CONFIG` routine.
