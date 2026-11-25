# Hardware description — MAMO NODE REV A

## Block diagram

```
            USB-C (charging)
                 │
 18650 ──► [ Charger + protection + boost module ]──► VO+ ──► SW1 ──► F1 (PTC 0.75 A) ──► +5V_SYS
 (BT1)        J1: B+ / B− / VO+ / VO−                     (ON/OFF)                          │
   │                                                                                       ├─► DWM3001CDK J10 pin 2 (5 V)
   └── BAT+ ──► R2/R3 divider ──► GP26 (BAT_SENSE)                                         ├─► C1 22 µF (local bulk)
                                                                                           ├─► R1 + D2 (POWER LED)
                                                                                           └─► D1 SS34 ──► Pico VSYS

 Pico GP2 ──► R4 220 Ω ──► Q1 AO3400A gate (R5 10 kΩ to GND)
                           Q1 drain ──► DWM_GND_SW ──► DWM J10 GND pins (6, 9, 14, 20, 25)
                           Q1 source ──► GND
```

## Power

| Stage | Part | Notes |
|---|---|---|
| Cell | 1 × 18650 Li-ion in an SMD holder (BT1) | Unprotected cells are fine: the module provides protection |
| Charger / protection / boost | Off-the-shelf "LX-LCBST"-type module (J1) | PT4056-class linear charger, HM5434-class cell protection and an MT3608-class adjustable boost. USB-C on the module. Output adjusted to **5.0 V** with a trimmer, which must be set under load and then sealed |
| Power switch | SW1, G-Switch MK-12C02-G020 (SPDT, 1 A) | Switches the 5 V output of the module |
| Fuse | F1, 0.75 A hold / 1.5 A trip PTC (1206) | Protects traces and headers against shorts on +5V_SYS |
| Pico supply | D1, SS34 Schottky, +5V_SYS → VSYS | Prevents back-feeding from the Pico USB port into the DWM/LED, and gives ~0.3 V of margin against an over-adjusted boost (VSYS max 5.5 V). VSYS ≈ 4.7 V in operation |
| Bulk capacitance | C1, 22 µF next to the DWM J10 header | Absorbs current peaks during UWB transmission |
| Power LED | D2 green 0805 + R1 1.5 kΩ (~2 mA) | Lit only when +5V_SYS is present after SW1 and F1 |

The charger module also has its own charge-status LEDs; the enclosure should expose them.

### Battery measurement

`BAT+ → R2 100 kΩ → GP26 → R3 100 kΩ → GND`, with C2 100 nF from GP26 to GND.

- Ratio 1/2: a full cell (4.2 V) gives 2.1 V at the ADC.
- 1 % resistors (C149504).
- Divider current ≈ 21 µA, drawn permanently from the cell.
- `V_bat = 2 × V_GP26`.

## DWM3001CDK hard reset (switched ground)

The DWM3001CDK is powered from +5V_SYS through J10 pin 2, but **its ground is switched** by Q1:

| GP2 | Q1 | DWM |
|---|---|---|
| LOW (or Pico not running) | off (R5 pulls the gate low) | **off** |
| HIGH | on | **on** |

All five ground pins of the DWM header (6, 9, 14, 20, 25) are tied to `DWM_GND_SW`.
None of them is connected to the system ground; otherwise the MOSFET would be bypassed.

> When the DWM's own USB port is connected to a PC at the same time as the Pico, the PC ground
> bypasses Q1. This only matters during debugging.

## NODE ID

6-position DIP switch (SW2) on GP10–GP15, each with a 10 kΩ pull-up to the Pico 3V3 output.

| Switch | GPIO | Weight |
|---|---|---|
| 1 | GP10 | 1 |
| 2 | GP11 | 2 |
| 3 | GP12 | 4 |
| 4 | GP13 | 8 |
| 5 | GP14 | 16 |
| 6 | GP15 | 32 |

- Switch **ON → GPIO reads 0** (active-low). The firmware inverts the logic.
- Valid IDs: **1–63**. ID **0** (all switches OFF) means "not configured" and must be treated as
  an error.

## Connectors

### J2 — DWM3001CDK header (2 × 13, 2.54 mm, female socket)

Matches the DWM3001CDK Raspberry Pi–compatible header J10. The footprint is a *PinHeader* layout
(Raspberry Pi side numbering) even though a female socket is mounted, because the DWM3001CDK
plugs in component-side up with its male pins pointing down.

| J10 pin | Net | Notes |
|---|---|---|
| 2 | +5V_SYS | DWM supply |
| 6, 9, 14, 20, 25 | DWM_GND_SW | Switched ground (Q1 drain) |
| 8 | UART: Pico GP0 → DWM | Direct connection, no series resistor |
| 10 | UART: DWM → Pico GP1 | Direct connection, no series resistor |
| 1, 17 (3V3), 4 (5V), 12 (RESET), others | not connected | The DWM 3V3 rail is never tied to the Pico 3V3 |

### J4 / J5 — Pico 2 W sockets (2 × 1 × 20, 2.54 mm, female)

J4 = Pico pins 1–20, J5 = Pico pins 21–40, placed on the standard Pico footprint
(17.78 mm between rows). RUN, 3V3_EN, VBUS, ADC_VREF and unused GPIOs are left unconnected.

### J1 — Charger / boost module

Custom footprint `uwb_node:PWR_MODULE_LCBST`: four through-holes (Ø1.0 mm) matching the module
pads plus two NPTH Ø2.2 mm holes for M2 screws through the module's half-moon notches.

| Pad | Signal |
|---|---|
| 1 | B+ |
| 2 | B− (cell negative; **not** system GND) |
| 3 | VO+ (5 V out) |
| 4 | VO− (system GND) |

### J3 — Reserved (not populated)

1 × 8, 2.54 mm: GP4, GP5, GP6, GP7, GP27, GP28, 3V3, GND. GP4/GP5 can be used as I²C0 SDA/SCL.

### Test points

| TP | Silkscreen | Net |
|---|---|---|
| TP1 | BAT+ | BAT+ |
| TP2 | GND | GND |
| TP3 | 5V | +5V_SYS |
| TP4 | EN | PICO2W_GP2_DWM_RESET |
| TP5 | DGND | DWM_GND_SW |

## Layout notes

- 100 × 90 mm, 2 layers. Power traces: 1.0 mm for the battery, 0.6 mm for the 5 V rail and the
  switched DWM ground, 0.25 mm for signals.
- The DWM3001CDK is rotated 90° so that its USB ports face the same edge as the Pico USB port,
  and its UWB module/antenna **overhangs the right board edge by ~9 mm** (no copper underneath).
- Rule-area keep-out (no copper pour, no vias) under the Pico 2 W antenna.
- The 18650 holder sits along the bottom edge, away from both antennas.
- Mounting holes H1 (top-left), H2 (bottom-left), H3 (bottom-right). There is no hole under the
  DWM; the enclosure should provide a support post there.
