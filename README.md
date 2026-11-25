# MAMO NODE — UWB Ranging Node (HW REV A)

Carrier board for a battery-powered **Ultra-Wideband (UWB) ranging node** built around a
**Raspberry Pi Pico 2 W** and a **Qorvo DWM3001CDK**. It turns a hand-wired prototype into a
clean, reproducible PCB designed in **KiCad 10** and assembled by **JLCPCB**.

> Design philosophy: REV A is deliberately the *same circuit as the working wired prototype*,
> moved to a PCB with a minimum of additions. No sophisticated power path, no extra sensors.

---

## Features

- **Plug-in modules:** the Pico 2 W and the DWM3001CDK sit on female headers and can be removed
  without soldering.
- **Single 18650 Li-ion cell** with an off-the-shelf charger + protection + 5 V boost module
  (USB-C charging, can charge and run at the same time).
- **Hardware power-cycle of the DWM3001CDK:** its ground is switched by a low-side N-MOSFET
  driven from a Pico GPIO (full hard reset, not just the RESET pin).
- **Node ID selectable in hardware:** 6-position DIP switch (IDs 1–63), so every node runs the
  same firmware.
- **Battery voltage monitoring** on an ADC pin.
- **Power switch, resettable fuse and power LED.**
- **UWB antenna overhangs the board edge** (no copper underneath), and a copper keep-out under
  the Pico 2 W Wi-Fi/BLE antenna.
- **Test points** for battery, 5 V rail, ground, DWM enable and the switched DWM ground.

## Repository structure

```
.
├── uwb_node_revA/            KiCad 10 project (schematic, PCB, project footprint library)
│   ├── uwb_node_revA.kicad_pro
│   ├── uwb_node_revA.kicad_sch
│   ├── uwb_node_revA.kicad_pcb
│   ├── uwb_node.pretty/      Custom / adapted footprints
│   └── production/           Fabrication outputs (Gerber ZIP, BOM, CPL) for JLCPCB
├── host/                     Python orchestrator (MQTT + Qorvo UCI) that runs on a PC/RPi
├── firmware/                 Pico 2 W firmware notes and interface (see firmware/README.md)
├── docs/
│   ├── hardware.md           Architecture, power, pinout, connectors
│   ├── assembly.md           What JLCPCB mounts and what is assembled by hand
│   ├── ordering-jlcpcb.md    How to regenerate the outputs and order the boards
│   ├── bring-up.md           First power-on and test checklist
│   └── known-issues.md       REV A limitations and ideas for REV B
├── LICENSE
└── README.md
```

## Board at a glance

| Item | Value |
|---|---|
| Size | 100 × 90 mm (+9 mm DWM module overhang on the right edge) |
| Stack-up | 2 layers, 1.6 mm FR-4, lead-free HASL |
| Ground | GND pour on both layers; keep-out under the Pico 2 W antenna |
| Mounting | 3 × M2 holes (H1, H2, H3) + 2 × M2 for the charger module |
| Supply | 1 × 18650 cell → charger/boost module → 5.0 V system rail |
| Main modules | Raspberry Pi Pico 2 W, Qorvo DWM3001CDK |

## Pico 2 W pin assignment

| GPIO | Pico pin | Function |
|---|---|---|
| GP0 | 1 | UART TX → DWM3001CDK J10 pin 8 |
| GP1 | 2 | UART RX ← DWM3001CDK J10 pin 10 |
| GP2 | 4 | DWM power control (HIGH = DWM on) |
| GP10–GP15 | 14–17, 19, 20 | NODE ID bits 0–5 (DIP switch, active-low) |
| GP26 | 31 | Battery voltage sense (÷2 divider) |
| GP4–GP7, GP27, GP28 | 6, 7, 9, 10, 32, 34 | Reserved, routed to J3 (not populated) |
| VSYS | 39 | System supply (from +5V_SYS through a Schottky diode) |
| 3V3 | 36 | Pull-ups for the NODE ID switch, J3 |

Details in [`docs/hardware.md`](docs/hardware.md).

## Opening the design

1. Install **KiCad 10**.
2. Open `uwb_node_revA/uwb_node_revA.kicad_pro`.
3. The project footprint library `uwb_node` is referenced through `${KIPRJMOD}`, so it is found
   automatically.

> Note: a few footprints and 3D models were imported from LCSC/EasyEDA with `easyeda2kicad`
> (see [`docs/known-issues.md`](docs/known-issues.md)). The PCB opens correctly because KiCad
> stores footprints inside the board file, but some 3D models may not render.

## Software

The [`host/`](host/README.md) folder contains the Python orchestrator that coordinates the
ranging sessions over MQTT. Qorvo's SDK files are required but not redistributed (see
`host/README.md`).

## Ordering

The `production/` folder contains the files used for the JLCPCB order (generated with the
**Fabrication Toolkit** plugin). See [`docs/ordering-jlcpcb.md`](docs/ordering-jlcpcb.md).

## Status

- [x] Schematic and PCB (REV A)
- [x] Fabrication files generated and boards ordered (JLCPCB, Standard PCBA)
- [ ] Bring-up and electrical validation
- [ ] Enclosure (3D-printed)
- [x] Host orchestrator published in `host/`
- [ ] Pico 2 W firmware published in `firmware/`

## License

Released under the [MIT License](LICENSE).

## Author

Designed by **Miguel Ángel**.
