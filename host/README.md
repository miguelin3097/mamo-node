# Host software — UWB orchestrator

Python tools that run on a PC (or a Raspberry Pi) to coordinate UWB ranging sessions between
the MAMO NODE boards (anchors) and the wearable devices (smartwatches), using **MQTT** and the
**Qorvo UCI** interface of the DWM3001CDK.

| File | Purpose |
|---|---|
| `orquestador_multi.py` | Multi-watch / multi-board orchestrator with a web dashboard. Configured through a JSON file |
| `orquestador_uwb.py` | Single-board orchestrator (e.g. running on a Raspberry Pi next to one board) |
| `watch_bridge_uci.py` | Listens to `uwb/target_mac` over MQTT and launches a ranging session |
| `fake_watch_02.py` | MQTT smartwatch simulator for testing without real watches |
| `orquestador_config.example.json` | Example configuration (copy it to `orquestador_config.json`) |
| `requirements.txt` | Python dependencies |

## Requirements

- Python 3.9+
- An MQTT broker reachable on the network (e.g. Mosquitto)
- DWM3001CDK boards flashed with Qorvo's **UCI over UART** firmware
- The Qorvo UCI Python helpers (see below)

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate    |    Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
```

## Qorvo files (not included)

The UCI Python library, its helpers, the `run_fira_twr.py` ranging script and the DWM3001CDK
firmware image are part of **Qorvo's DW3_QM33 SDK (v1.0.2)** and are distributed under Qorvo's
own license (`LicenseRef-QORVO-2`), so they are **not redistributed here**.

Obtain the SDK from Qorvo and copy into this `host/` folder:

```
host/
├── uci/               ← from the SDK (UCI Python package)
├── uqt_utils/         ← from the SDK
└── run_fira_twr.py    ← from the SDK (FiRa two-way-ranging script)
```

Flash the DWM3001CDK with the SDK's **UCI FreeRTOS UART** firmware image
(`DWM3001CDK_..._UCI-FreeRTOS_UART.hex`).

> Some scripts default to `run_fira_twr_backup.py` (a locally modified copy). Use `--script`
> (or `run_script` in the JSON config) to point to the script you actually use.

## Usage

### Multi-board orchestrator

```bash
cp orquestador_config.example.json orquestador_config.json   # then edit it
python orquestador_multi.py --config orquestador_config.json
```

Main configuration fields:

| Field | Meaning |
|---|---|
| `mqtt.broker`, `mqtt.port` | MQTT broker |
| `orchestrator.ranging_time` | Duration of each ranging session (s) |
| `orchestrator.data_dir` | Output folder for the measurement files (`.tsv`) |
| `orchestrator.run_script` | Ranging script to launch |
| `boards[]` | One entry per board: `anchor`/`my_mac` (UWB short address), `serial_port` (`COMx`, `/dev/ttyACMx` or `uart:socket://<ip>:9000` for a board reached over the network), `label` |
| `cycle.watches[]` | Logical IDs of the watches to serve in round-robin |
| `dashboard` | Web dashboard host/port (default `http://<pc-ip>:8080/`) |

### Single board

```bash
python orquestador_uwb.py --broker <broker-ip> --serial-port /dev/ttyACM0 \
    --my-mac 00:01 --anchor 00:01 --watch-id watch_01 --time 10
```

### Watch simulator

```bash
python fake_watch_02.py --broker <broker-ip> --watch-id watch_02
```

## MQTT topics used

| Topic | Direction | Content |
|---|---|---|
| `uwb/presence/<watch>` | watch → orchestrator | Watch presence / availability |
| `uwb/session/<watch>` | watch ↔ orchestrator | Session negotiation (prepare / start / stop) |
| `uwb/pico/<board>/status` | board → orchestrator | Board (Pico) health status |
| `uwb/target_mac` | → bridge | Target UWB address for `watch_bridge_uci.py` |

## Output

Measurements are written to `data/` as `<timestamp>.<watch_id>.tsv`. The `data/` folder is
ignored by Git.
