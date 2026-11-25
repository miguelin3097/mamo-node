# Known issues and notes — REV A

## Design / manufacturing

- **BT1 battery holder is not reflow-safe.** JLCPCB cannot assemble C2988620 with the standard
  profile; it must be hand-soldered. For REV B, choose a reflow-rated holder or keep it as a
  hand-assembled part.
- **Charger module is a third-party board.** It is mounted on machined pins plus two M2 screws.
  Its boost output is set with a trimmer (adjustable up to ~28 V); it must be set to 5.0 V and
  sealed. VSYS is protected by the SS34 drop (VSYS max 5.5 V).
- **No mounting hole under the DWM3001CDK** (H4 was removed for easier assembly). The enclosure
  needs a support post in that area.
- **Unused power in OFF state:** SW1 switches the boost output, so the boost stays powered from
  the cell when the node is off (~mA level) plus ~21 µA in the battery divider. Remove the cell
  for long storage.
- **No reverse-polarity protection** on the cell: rely on the silkscreen and the enclosure
  marking.

## KiCad project portability

- SW1 and SW2 footprints point to an `easyeda2kicad` library that is not part of this
  repository. The PCB opens correctly (footprints are embedded in the board file), but
  *Update PCB from schematic* on another computer will complain. Fix: copy the footprints into
  `uwb_node.pretty` and reassign them.
- Some 3D models (battery holder, SW1, SW2) use the `${EASYEDA2KICAD}` path variable and will
  not render unless the models are added to the repository and their paths updated.
- The schematic keeps the Raspberry Pi Pico W symbol (`A1`) for documentation only; it is
  excluded from the board and BOM. The actual footprints are the sockets J4 and J5.

## Ideas for REV B

- Integrate charger + boost (avoid the third-party module and its trimmer).
- Reflow-compatible battery holder.
- Optional switch on the battery side for zero standby drain.
- Mounting hole/support near the DWM3001CDK.
