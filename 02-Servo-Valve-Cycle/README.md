# 02 – Multi-Position Valve Cycle with Servo

**Platform:** Allen-Bradley MicroLogix 1100 · RSLogix 500 · RSEmulate 500 · *simulated*

## Overview
A cam-driven control valve in a water-treatment skid. When the host system calls for a cycle, a servo turns the cam from HOME to FILL (hold 10 s), DRAIN (hold 20 s), FLUSH (hold 10 s), then back to HOME. Cam-operated limit switches report the valve position. A reset button aborts the cycle and returns the valve to HOME.

## I/O map
| Address | Description |
|---|---|
| `I:0/0` | Home switch |
| `I:0/1` | Fill switch |
| `I:0/2` | Drain switch |
| `I:0/3` | Flush switch |
| `I:0/4` | Cycle call from host |
| `O:0/0` | Servo (energize to turn cam) |
| `B3:0/0` | Reset / interrupt button |
| `N7:0` | Valve position: 0 HOME, 1 FILL, 2 DRAIN, 3 FLUSH, 4 TRAVELLING |

## Program structure
| File | Routine | Role |
|---|---|---|
| 2 | `MAIN` | Calls IO, CONTROLS, STATUS |
| 3 | `IO` | Copies physical inputs to internal bits and the servo bit to the output |
| 4 | `CONTROLS` | Hold timers (10 / 20 / 10 s), servo run logic, reset interrupt |
| 5 | `STATUS` | Writes the position code to `N7:0` |

## How it works
- **Servo run logic:** the servo bit is energized by the cycle call, and it re-energizes when a hold timer completes. It stays on until the HOME switch is reached.
- **Hold timers:** reaching FILL, DRAIN or FLUSH starts the matching on-delay timer (10 / 20 / 10 s). The servo pauses while the timer is timing and restarts when it finishes.
- **Reset:** the reset button clears the hold timers so the servo resumes toward HOME, which aborts the cycle.
- **Position reporting:** five `MOV` rungs write 0–3 when the corresponding switch is made, and 4 when no switch is made (in transit).

## Test procedure (from the project brief)
1. Run: servo energized, `N7:0` = 4 (looking for home).
2. Force HOME on: servo off, `N7:0` = 0.
3. Pulse `I:0/4`: servo on, `N7:0` still 0.
4. Force HOME off, FILL on: `N7:0` = 1, servo off for 10 s, then restarts.
5. FILL off, DRAIN on: `N7:0` = 2, servo off for 20 s, then restarts.
6. DRAIN off, FLUSH on: `N7:0` = 3, servo off for 10 s, then restarts.
7. FLUSH off, HOME on: `N7:0` = 0, servo off and stays off.
8. Bonus: call a cycle, press reset, drive the valve to HOME: servo stops.

## Concepts demonstrated
Position-feedback sequencing with limit switches, timed holds, status reporting, abort/reset handling, one-shots.

## Possible improvements
Power-loss recovery (resume mid-cycle), manual positioning, bi-directional servo, configurable cycle order and dwell times, and a "travel timeout" fault if the valve fails to reach a position.
