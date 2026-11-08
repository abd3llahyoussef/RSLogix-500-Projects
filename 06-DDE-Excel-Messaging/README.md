# 06 – Mode/Function Sequencer with Excel (DDE) Data Pull

**Platform:** Allen-Bradley MicroLogix 1100 · RSLogix 500 · RSEmulate 500 · RSLinx (DDE topic) · Microsoft Excel · *simulated*

## Overview
A machine cycles continuously through two modes, each running two functions of 10 seconds: **M1F1 → M1F2 → M2F1 → M2F2 → …**. The PLC stores the current mode and function names as strings, counts every cycle, and Excel pulls the data on demand through a DDE link served by RSLinx.

## Memory map
| Address | Description |
|---|---|
| `ST9:0` | Current mode ("Mode-1" / "Mode-2") |
| `ST9:1` | Current function ("F1" / "F2") |
| `ST9:2` | Mode + function (concatenated) |
| `ST9:3`–`ST9:6` | Template strings used to build the names |
| `N7:0`–`N7:3` | Cycle counters: M1F1, M1F2, M2F1, M2F2 |
| `T4:0`–`T4:3` | 10 s step timers |
| `B3:0/2`, `/3`, `/5`, `/6` | M1F1, M1F2, M2F1, M2F2 step bits |

## Program structure
| File | Routine | Role |
|---|---|---|
| 2 | `MAIN` | Calls the routines |
| 3 | `CYC COUNT` | One-shot–triggered counters for each step |
| 4 | `CONTROLS` | Chained 10 s timers, string copy (`COP`) and concatenation (`ACN`) |

## How it works
1. Four chained on-delay timers (each 10 s) step the machine through M1F1, M1F2, M2F1, M2F2 and back to Mode 1.
2. On entering each step, `COP` copies the mode and function template strings into `ST9:0` and `ST9:1`, and `ACN` joins them into `ST9:2`.
3. A one-shot on each step bit adds 1 to that step's counter, so the counters read (0,0,0,0), (1,0,0,0), (1,1,0,0), … as the brief expects.
4. **Excel side:** an RSLinx DDE topic exposes the PLC data table; an Excel macro assigned to a "Refresh data" button reads the strings and integers into the sheet.

## Concepts demonstrated
Timer-chained state sequencing, string handling (`COP`, `ACN`), counters with one-shots, and plant-floor data exchange with Excel through RSLinx DDE and VBA macros.

## Items to add to this folder
- `screenshots/excel-sheet.png` (the refreshed sheet)
- The DDE topic name and the cell/macro formulas you used
