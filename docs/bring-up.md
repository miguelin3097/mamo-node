# Bring-up checklist — MAMO NODE REV A

Validate **one board** completely before assembling the rest.

## 1. Visual inspection

- [ ] No solder bridges on SW2, Q1 or the sockets.
- [ ] D1 and D2 orientation as in the silkscreen.
- [ ] BT1 soldered with **+ on the right**.
- [ ] Charger module output previously set to 5.0 V and trimmer sealed.

## 2. Power, without Pico and without DWM

| Step | Measure | Expected |
|---|---|---|
| Cell inserted, SW1 OFF | TP1 (BAT+) – TP2 (GND) | 3.0–4.2 V (cell voltage) |
| SW1 OFF | TP3 (5V) – TP2 | ≈ 0 V, LED off |
| SW1 ON | TP3 (5V) – TP2 | ≈ 5.0 V, **POWER LED on** |
| SW1 ON | VSYS pin of the Pico socket (J5 pin 19) | ≈ 4.6–4.8 V |
| SW1 ON | 5 V pin of the DWM socket (J2 pin 2) | ≈ 5.0 V |
| SW1 ON | TP5 (DGND) – TP2 | floating (DWM ground is switched off by default) |
| USB-C on the module | Module charge LED | Charging indication |

## 3. Pico only

- [ ] Pico boots from the battery with SW1 ON.
- [ ] NODE ID read correctly for a few DIP combinations (remember: ON = 0 at the GPIO).
- [ ] ID 0 (all OFF) reported as an error.
- [ ] Battery reading ≈ cell voltage (`2 × V_GP26`).
- [ ] TP4 (EN) toggles with GP2.

## 4. Pico + DWM3001CDK

- [ ] GP2 LOW → DWM off; GP2 HIGH → DWM boots (TP5 ≈ 0 V when on).
- [ ] UART communication on GP0/GP1.
- [ ] Power-cycle sequence works repeatedly (UART pins high-Z before switching off).
- [ ] UWB ranging between two nodes.

## 5. Optional measurements

- [ ] Current consumption (idle / ranging) to confirm the 0.75 A PTC choice.
- [ ] Charging while operating.
