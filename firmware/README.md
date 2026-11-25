# Firmware — Raspberry Pi Pico 2 W

Firmware for the MAMO NODE carrier. *(Source code to be added.)*

## Hardware interface

| Signal | GPIO | Notes |
|---|---|---|
| UART TX → DWM | GP0 | 3.3 V, direct to DWM3001CDK J10 pin 8 |
| UART RX ← DWM | GP1 | direct from DWM3001CDK J10 pin 10 |
| DWM power enable | GP2 | HIGH = DWM powered (low-side ground switch). Pulled low by hardware at reset |
| NODE ID bit 0–5 | GP10–GP15 | External 10 kΩ pull-ups; switch ON = 0 (active-low) |
| Battery sense | GP26 / ADC0 | `V_bat = 2 × V_adc` |
| Reserved | GP4–GP7, GP27, GP28 | On J3 (not populated). GP4/GP5 usable as I²C0 |

## Required behaviour

1. **Startup**
   - Read NODE ID: `id = ~(GP15..GP10) & 0x3F`.
   - If `id == 0`, report the error (serial) and do not start normal operation.
2. **Power on the DWM**
   - Keep GP0/GP1 as inputs (high-Z).
   - Set GP2 HIGH, wait for the DWM to boot, then initialise the UART.
3. **Power off / hard reset of the DWM**
   - Set GP0/GP1 as inputs (high-Z) to avoid back-powering the DWM through the UART.
   - Set GP2 LOW and wait before powering it on again.
4. **Battery**
   - Read ADC0 and multiply by 2 (plus calibration if needed).
