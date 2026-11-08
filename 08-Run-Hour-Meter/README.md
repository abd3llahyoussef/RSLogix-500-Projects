# 08 – Run-Hour Meter (Retentive Timing and Cascaded Counters)

**Platform:** Allen-Bradley MicroLogix 1100 · RSLogix 500 · RSEmulate 500 · *simulated*

## Overview
A run-time meter that accumulates how long a system has been running. It measures seconds with a retentive timer and cascades counters for minutes, hours and thousands of hours, so very long run times can be tracked with 16-bit registers. A reset button clears everything.

## Memory map
| Address | Description |
|---|---|
| `I:0/0` | System running signal |
| `B3:0/0` | Reset button |
| `B3:0/4` | "System running" seal-in bit |
| `T4:0` | Retentive seconds timer (RTO) |
| `C5:0` | Minutes counter (preset 60) |
| `C5:1` | Hours ×1 counter (preset 1000) |
| `C5:2` | Hours ×1000 counter (preset 1000) |
| `N7:0` – `N7:3` | Seconds, minutes, hours (×1), hours (×1000) for display / HMI |

## Program structure
| File | Routine | Role |
|---|---|---|
| 3 | `IN` | Copies the system signal input to a bit |
| 4 | `CONTROLS` | Running bit, timer, counter cascade, reset |

## How it works
1. A one-shot on the signal produces a trigger, and a seal-in rung holds the "running" bit true while the system signal stays on.
2. A **retentive timer (RTO)** counts seconds only while running and *keeps* its accumulated value when the system stops, so stoppages do not lose time.
3. When the seconds timer completes, it increments the minutes counter and resets itself.
4. The minutes counter feeds the hours counter, which feeds the ×1000 hours counter. Each stage is reset by the stage that follows it.
5. Each accumulator is copied to `N7:0`–`N7:3`, which are the values shown on the HMI.
6. The reset button (through a one-shot) zeroes the integers, all counters, and the timer.

Total run time = `N7:3 × 1000 h + N7:2 h + N7:1 min + N7:0 s`.

## Concepts demonstrated
Retentive timers, cascaded counters, seal-in logic, one-shots, subroutine structure, and data organized for HMI display.

## Notes for improvement
The seconds timer's done bit is reached at an accumulated value equal to its preset, so check that the preset gives exactly 60 s per minute in your final version (a preset of 60 counts a full minute). Also consider retaining the counters across power cycles by using retentive data files.
