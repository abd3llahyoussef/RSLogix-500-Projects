# 03 – Digital Rotameter Flow Calculation

**Platform:** Allen-Bradley MicroLogix 1100 · RSLogix 500 · RSEmulate 500 · *simulated*

## Overview
A digital rotameter in an oil pipeline sends one pulse for every 6.3 gallons (its k-factor). The program simulates the pulse with a timer, accumulates gallons over a one-minute window, and stores the flow rate in gpm.

## Memory map
| Address | Description |
|---|---|
| `B3:0/0` | Rotameter pulse bit (pulsed by a timer in place of a real input) |
| `T4:0` | Pulse timer (12 s preset in the brief) |
| `T4:1` | One-minute measurement window timer |
| `F8:1` | k-factor (6.3 gal/pulse) |
| `F8:0` | Flow / accumulated reading (gpm) |

## Logic summary
1. `T4:1` times the measurement window; `T4:0` generates the pulse period.
2. When `T4:0` completes, a one-shot sets the pulse bit and resets `T4:0` to start the next period.
3. On each pulse a one-shot adds the k-factor (`F8:1`) into `F8:0`.
4. When the window timer completes it restarts, and the accumulator is cleared for the next window.

## Expected values (from the brief)
- Pulse every **12 s** → 5 pulses/min × 6.3 gal = **31.5 gpm**.
- Pulse every **2 s** → 30 pulses/min × 6.3 gal = **189 gpm**.

## Concepts demonstrated
Pulse-based flow measurement, k-factor scaling, timer-generated test signals, one-shot edge detection, float math.

## Notes for improvement
This design reads the flow once per window and clears the accumulator. A smoother alternative is to latch the finished window total into a separate "flow" register before clearing, so the displayed value stays steady between windows. Document which approach your final program uses and re-test it against the 31.5 / 189 gpm checks.
