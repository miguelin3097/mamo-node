# Assembly — MAMO NODE REV A

## What JLCPCB assembles (Standard PCBA, top side)

| Ref | Part | LCSC | Notes |
|---|---|---|---|
| C1 | 22 µF 25 V X5R 0805 | C45783 | Basic |
| C2 | 100 nF 50 V X7R 0805 | C49678 | Basic |
| D1 | SS34 Schottky, SMA | C8678 | Basic |
| D2 | Green LED 0805 | C2297 | Basic |
| F1 | PTC 0.75 A hold, 1206 (BSMD1206-075-30V) | C976305 | Extended |
| J2 | Female header 2 × 13, 2.54 mm, 8.5 mm | C64320 | Extended, THT |
| J4, J5 | Female header 1 × 20, 2.54 mm, 8.5 mm | C2905423 | Extended, THT |
| Q1 | AO3400A N-MOSFET, SOT-23 | C20917 | Basic |
| R1 | 1.5 kΩ 0805 | C4310 | Basic |
| R2, R3 | 100 kΩ 1 % 0805 | C149504 | Basic |
| R4 | 220 Ω 0805 | C17557 | Basic |
| R5–R11 | 10 kΩ 0805 | C17414 | Basic |
| SW1 | Slide switch SPDT, MK-12C02-G020 | C963206 | Extended |
| SW2 | DIP switch 6 positions, DSIC06LSGET | C54952 | Extended |

## What is assembled by hand

| Ref | Part | Why |
|---|---|---|
| BT1 | 18650 SMD holder, MYOUNG BH-18650-B1BA002 (LCSC C2988620) | Its plastic is not rated for the 240 °C reflow profile; JLCPCB leaves it unpopulated |
| J1 | Charger / protection / boost module (LX-LCBST type) | Not available as a JLCPCB part |
| J3 | Reserved header | Not populated (DNP) |

### Battery holder (BT1)

- Use a **soldering iron with a wide chisel tip** at ~350–380 °C and flux.
- Pre-tin one pad, place the holder, solder that terminal, then the other one.
- Keep each joint short to avoid heating the plastic.
- **Do not** use hot air, a hot plate or a reflow oven.
- Orientation: **+ to the right** (BAT+ silkscreen). The spring contact is the negative terminal.

### Charger module (J1)

1. Set the module output to **5.0 V** with a representative load and seal the trimmer
   (nail polish or thread locker).
2. Solder **4 machined round-pin header pins** (pin Ø0.5–0.6 mm; the module holes are 0.8 mm)
   into the B+, B−, VO+ and VO− holes of the module.
3. Insert the module into J1 with its **USB-C towards the left board edge** and solder the pins
   from the bottom.
4. Fix the USB-C side with **2 × M2 screws** through the module's half-moon notches and the two
   NPTH holes, using spacers of the same height as the pin plastic (≈ 2.5–3 mm, 3D-printed:
   ID ≈ 2.3 mm, OD ≈ 4.5 mm). Nylon or metal screws are fine if the notches are not plated.

### Modules on sockets

- **Pico 2 W:** male headers soldered pointing down; USB towards the top board edge.
- **DWM3001CDK:** component side up, male header pointing down; its UWB module overhangs the
  right edge.

## Mechanical parts per board

| Item | Qty |
|---|---|
| Machined round pins (charger module) | 4 |
| M2 screws + nuts + spacers (charger module) | 2 |
| M2 screws for H1–H3 (+ M2 heat-set inserts in the enclosure) | 3 |
| 18650 cell | 1 |
