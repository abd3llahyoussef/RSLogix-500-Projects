# 07 – Pump Control with HOA, Protection and Alarm Management

**Platform:** Allen-Bradley MicroLogix 1100 · RSLogix 500 · RSEmulate 500 · *simulated*

## Overview
A pump that cycles automatically (30 s on, 10 s off) with **HAND / OFF / AUTO** control, protected by a flow switch and a pressure sensor. Faults stop the pump, latch an alarm, drive a fault indicator, and can be silenced and reset by the operator.

## I/O map
| Address | Description |
|---|---|
| `I:0/0` | Flow switch |
| `I:0/1` | Alarm reset button |
| `I:0/2` | Alarm silence button |
| `B3:0/0`, `/1`, `/2` | HAND, OFF, AUTO push buttons |
| `N7:0` | Pressure sensor raw (0–16383 = 0–55 psi) |
| `N7:1` | Pressure (psi, scaled with `SCP`, clamped to 0–55) |
| `N7:2` | Mode: 0 OFF, 1 HAND, 2 AUTO |
| `N7:3` | Previous mode (restored after HAND is released) |
| `O:0/0` | Pump |
| `O:0/1` | Flow fault indicator |
| `O:0/2` | Pressure fault indicator |

## Program structure
| File | Routine | Role |
|---|---|---|
| 3 | `IO` | Inputs, pressure scaling and clamp, outputs |
| 4 | `CONTROLS` | 30 s run / 10 s rest cycle, flow & pressure check timer |
| 5 | `ALARM` | Alarm bits, notification bits, reset and silence |
| 6 | `CONDITIONS` | HOA mode selection, previous-mode memory, mode forced to OFF on fault |

## Behavior
- **AUTO:** the pump cycles 30 s on / 10 s off. It cannot run in AUTO while an alarm is active.
- **HAND:** the pump runs only while HAND is pressed; on release it returns to the mode it was in (OFF or AUTO), stored in `N7:3`. In this design HAND still works during an alarm.
- **Flow protection:** if the pump is running and the flow switch has not made after the check time, a flow fault is raised, the cycle stops, and the mode drops to OFF.
- **Pressure protection:** if pressure exceeds 30 psi after the check time, a pressure fault is raised and the mode drops to OFF.
- **Alarm handling:** the alarm bit seals in until the reset button is pressed. The *notification* bit (the indicator) is separate and can be silenced.

## Test procedure (from the project brief)
1. Start: no cycle, no faults. `N7:0` = 8192 → ≈ 27.5 psi.
2. HAND on with no flow: pump runs, trips on the flow fault and `O:0/1` energizes. Silence, then check the indicator clears.
3. AUTO with the fault active: pump must not cycle.
4. Reset (`I:0/1`), force flow switch on, press AUTO: pump cycles on/off.
5. HAND during the cycle: pump runs continuously, then returns to cycling on release.
6. `N7:0` = 10000 → ≈ 33.5 psi: pump shuts down and `O:0/2` energizes.

## Concepts demonstrated
HOA logic with state memory, alarm bits vs. notification bits, latch/reset/silence behavior, equipment protection (cavitation and over-pressure), analog scaling with range clamping, timed cycling.

## Design decision to explain in interviews
Whether HAND should work during an active alarm is intentionally left open by the brief. Be ready to explain your choice.
