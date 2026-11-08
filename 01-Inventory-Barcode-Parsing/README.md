# 01 – Barcode Inventory Control (String Parsing)

**Platform:** Allen-Bradley MicroLogix 1100 (Bul. 1763) · RSLogix 500 · RSEmulate 500 · *simulated*

## Overview
A PLC-based inventory tracker. A barcode scanner writes a string of the form `PPP-QQ:D` into a string register. The program extracts the part number (PPP), quantity (QQ) and direction (D: 1 = incoming, 2 = outgoing), then updates the on-hand count for three parts (123, 456, 789). Unrecognized part numbers are ignored without faulting.

## I/O and memory map
| Address | Purpose |
|---|---|
| `ST9:0` | Raw barcode string from the scanner |
| `ST9:1`, `ST9:2` | Delimiter strings used for searching (`-` and `:`) |
| `ST9:3`, `ST9:4`, `ST9:5` | Extracted part, quantity and direction strings |
| `N7:0`, `N7:1`, `N7:2` | On-hand quantity: part 123, 456, 789 |
| `N7:3`–`N7:6` | Delimiter positions and start indexes |
| `N7:7`, `N7:8`, `N7:9` | Parsed part number, quantity, direction (integers) |
| `N7:10`–`N7:12` | Previous scan values (for change detection) |
| `B3:0/3` | "New input" bit |

## Program structure
| File | Routine | Role |
|---|---|---|
| 2 | `MAIN` | First-pass initialization, calls the subroutines |
| 3 | `CONTROLS` | String extraction (`AEX`, `ASC`) |
| 4 | `CTRLSTATUS` | String-to-integer conversion (`ACI`), part and direction decoding, add/subtract |
| 5 | `INPUT CHK` | Compares new values with the previous scan, raises `B3:0/3` once |

## How it works
1. `AEX` copies characters 1–3 into `ST9:3` (part number).
2. `ASC` finds the position of `-`; the quantity starts one character later and is extracted with `AEX`.
3. `ASC` finds `:`; the direction is the next character.
4. `ACI` converts the three strings into integers.
5. When the "new input" bit is set, `EQU` instructions match the part number (123 / 456 / 789) and direction, then `ADD` (incoming) or `SUB` (outgoing) updates the matching on-hand register.
6. A part number that matches none of the branches changes nothing, so a bad scan cannot fault the program.
7. `INPUT CHK` compares the parsed part, quantity and direction against the previous scan; any difference raises the one-shot "new input" bit, so each scan is processed once per scan cycle rather than every PLC scan.

## Test procedure (from the project brief)
| Step | Action | Expected |
|---|---|---|
| 1 | Run in Emulate | `N7:0`–`N7:2` = 0 |
| 2 | `ST9:0` = `123-10:1` | `N7:0` = 10 |
| 3 | Same string again | `N7:0` = 20 |
| 4 | `456-15:1` | `N7:1` = 15 |
| 5 | `789-30:1` | `N7:2` = 30 |
| 6 | `123-19:2`, `456-14:2`, `789-29:2` | all three = 1 |
| 7 | `149-1:1` | nothing changes, no fault |

Record your own results and screenshots in `/screenshots`.

## Concepts demonstrated
String instructions (`AEX`, `ASC`, `ACI`), delimiter-based parsing, change detection with one-shots, defensive handling of invalid input, structured subroutine design.

## Known limitations / possible improvements
- Quantity extraction uses a fixed 2-character width, matching the `QQ` format in the brief.
- Change detection compares each scan with the previous one, so back-to-back *identical* scans need the scanner write to be detected explicitly (for example by checking that `ST9:0` is non-empty, processing it, then clearing it). Worth mentioning in interviews as a design trade-off.
