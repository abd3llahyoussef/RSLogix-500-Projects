# PLC & Industrial Automation Portfolio

Ten simulated automation projects by **Abdallah Youssef**, covering three PLC platforms:

| Platform | Environment | Projects |
|---|---|---|
| Allen-Bradley MicroLogix 1100 | RSLogix 500, RSEmulate 500, RSLinx | 01–08 |
| Siemens | TIA Portal, WinCC Runtime, GRAPH | 09 |
| ABB CODESYS | IEC 61131-3 (SFC, LD, FBD, ST, CFC, IL) | 10 |

All projects were developed and tested in simulation or emulation (Sep–Oct 2026), with no physical hardware. They are built from training project briefs, with the programs written by me.

## Projects
| # | Project | What it shows |
|---|---|---|
| 01 | [Barcode inventory control](01-Inventory-Barcode-Parsing) | String parsing, change detection, input validation |
| 02 | [Servo valve cycle](02-Servo-Valve-Cycle) | Position-feedback sequencing, timed holds, abort |
| 03 | [Rotameter flow calculation](03-Rotameter-Flow) | Pulse-based flow, k-factor, float math |
| 04 | [O₂ analyzer auto-calibration](04-O2-Sensor-Calibration) | Two-point calibration, retuning scaling at runtime |
| 05 | [Vacuum control without PID](05-Vacuum-PID-Substitution) | Tiered step control, dead-band, direction handling |
| 06 | [Mode sequencer with Excel DDE](06-DDE-Excel-Messaging) | Strings, counters, RSLinx DDE, VBA |
| 07 | [Pump HOA and protection](07-Pump-Protection-HOA) | HOA, alarms, interlocks, scaling and clamping |
| 08 | [Run-hour meter](08-Run-Hour-Meter) | Retentive timer, cascaded counters |
| 09 | [Hydroelectric plant + 6-screen HMI](09-Hydroelectric-TIA-WinCC) | GRAPH sequence, 7 alarms, HOA, WinCC HMI |
| 10 | [Tank process, 5 IEC languages](10-Tank-Process-CODESYS) | SFC with LD, FBD, ST, CFC, IL |

## Skills demonstrated
Sequence control · alarm management (delay, latch, inhibit, reset, silence) · HOA · interlocks · analog scaling (4–20 mA, 0–16383, 0–27648) · string handling · retentive timing · custom non-PID control · HMI design · PLC-to-Excel data exchange.

## Folder layout
Each project folder contains a `README.md` and, once added, `screenshots/` and the project files (`.rss`, `.zap`, `.project`, etc.).

## Related work
Industrial IoT: Modbus TCP ↔ MQTT gateway, ESP8266/Arduino equipment monitoring, and climate telemetry pipelines (see my other repositories).

## Contact
abdallahjoseph362@gmail.com · [LinkedIn](https://www.linkedin.com/in/abdallah-youssef-578759207/) · [Portfolio](https://portfolio-lovat-three-67.vercel.app/)
