# Ordering from JLCPCB

## 1. Generate the fabrication files

1. Open the project in KiCad 10 and run **DRC** (0 errors expected; silkscreen-at-edge and
   library-mismatch warnings are known and excluded).
2. Press **B** to refill zones.
3. Run the **Fabrication Toolkit** plugin with:
   - *Apply automatic component translations*: on
   - *Apply automatic fill for all zones*: on
   - *Exclude DNP components from BOM*: on
   - *Plot all active layers*: off
4. The plugin writes `uwb_node_revA/production/`:
   - `uwb_node_revA.zip` — Gerber + drill files
   - `uwb_node_revA_bom.csv` — BOM with LCSC part numbers
   - `uwb_node_revA_positions.csv` — pick-and-place (CPL)

> Important: when updating the PCB from the schematic (**F8**), keep
> *"Delete footprints with no symbols"* **unchecked**, otherwise the mounting holes H1–H3
> (board-only footprints) are deleted.

## 2. PCB options

| Option | Value |
|---|---|
| Layers | 2 |
| Thickness | 1.6 mm |
| Surface finish | HASL lead-free |
| Solder mask | any (REV A: black) |
| Quantity | min. 5 |

## 3. Assembly options

| Option | Value |
|---|---|
| PCBA type | **Standard** (required for the THT sockets) |
| Side | Top |
| Tooling holes | Added by JLCPCB |

Upload the BOM and CPL files and review every line (stock, Basic/Extended).

## 4. Placement review (do not skip)

Check polarity/orientation in the JLCPCB viewer before paying:

| Ref | Check |
|---|---|
| D1 | Cathode band towards the Pico (left) |
| D2 | Cathode towards GND |
| Q1 | Single pin (drain) on the single pad |
| SW1 | Actuator towards the left board edge |
| SW2 | "ON" towards the top (towards the charger module) |
| J2, J4, J5 | Exactly over their holes, vertical |

Rotation corrections are stored in the schematic as the field `JLCPCB Rotation Offset`
(J4 and J5: 90°), which the Fabrication Toolkit applies automatically.

## 5. Notes from the REV A order

- **BT1 (C2988620)** was flagged by JLCPCB as not heat-resistant. It was left unpopulated
  (option C) and is soldered by hand. Mark BT1 as *Exclude from BOM* and *Exclude from position
  files* before the next order.
- R2/R3 originally used C17407 (out of stock); replaced by the Basic part **C149504**.
- Unpopulated parts are not shipped with the boards.
