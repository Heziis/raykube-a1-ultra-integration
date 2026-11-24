# Raykube A1 Ultra (Tuya BLE)

**[English](#english)**

## English

Author: **Jarosław Królikowski** ([@jaroslaw-krolikowski](https://github.com/jaroslaw-krolikowski))

## About this fork

Fork maintainer: **Hezi Israeli** ([@Heziis](https://github.com/Heziis))

This fork is identical to upstream, except for two reliability fixes in the BLE connection layer,
developed and tested on a live Home Assistant installation with the Raykube A1 Ultra:

1. **Bounded startup wait** (`custom_components/tuya_local_ble/__init__.py`):
   the initial BLE connect+setup during config-entry startup is now wrapped in a 90-second
   `asyncio.wait_for` bound. Previously a lock that did not answer in time left the config entry
   stuck half-loaded until Home Assistant restarted; now the attempt fails cleanly with
   `ConfigEntryNotReady`, and Home Assistant retries setup automatically.
2. **Cancellation-safe error handling** (`custom_components/tuya_local_ble/tuya_ble/tuya_ble.py`):
   bare `except:` clauses were replaced with `except Exception:` so `asyncio.CancelledError`
   (raised during shutdown/reload) is no longer swallowed, which could corrupt connection state
   and break later reconnect attempts.

Upstream credit: original integration by **Jarosław Królikowski**
([jaroslaw-krolikowski/raykube-a1-ultra-integration](https://github.com/jaroslaw-krolikowski/raykube-a1-ultra-integration)),
itself a fork of [ShonP40/Tuya-BLE](https://github.com/ShonP40/Tuya-BLE). License: MIT (unchanged, see LICENSE).

---

## Installation

### HACS (recommended)

1. In HACS, open the menu and choose **Custom repositories**.
2. Add `https://github.com/Heziis/raykube-a1-ultra-integration` with type **Integration**.
3. Find "Raykube A1 Ultra (Tuya BLE)" in HACS and install it.
4. Restart Home Assistant.

### Manual

1. Copy `custom_components/tuya_local_ble` into your Home Assistant `custom_components` directory.
2. Restart Home Assistant.

Then add the lock: **Settings > Devices & services > Add integration > Raykube A1 Ultra (Tuya BLE)**.
Full setup (including `devices.json` and where to get the keys): [docs/raykube-a1-ultra.en.md](docs/raykube-a1-ultra.en.md).



A fork of [ShonP40/Tuya-BLE](https://github.com/ShonP40/Tuya-BLE) (MIT) that gives Home Assistant local
control of the **Raykube A1 Ultra** lock (`jtmspro`, `hc7n0urm`, `rlyxv7pe`)
over Bluetooth, with no cloud and no gateway.

Lock entities:

- lock and unlock (`lock`),
- calibration (button, and the card with confirmation; at most once every 2 minutes),
- mute and unmute (switch),
- volume level (select),
- auto-lock and its delay (switch + number in seconds),
- arming mode: an HA notification when the lock is unlocked by hand (blueprint `blueprints/automation/a1ultra/lock_arming_alarm.yaml`),
- "Raykube Lock" dashboard card (the button locks and unlocks, unlocking asks for confirmation; a tap on the tile shows details),
- diagnostics: BLE connection state with the last error, every DP with names and change history, a *Download diagnostics* file,
- the `tuya_local_ble.set_datapoint` action to write any DP.

Command safety: a command that does not reach the lock in time is dropped, never sent later; the state
shown is the one the lock reports; locking is always possible. Details in the documentation.

The UI (entities, options, card, blueprint) is available in English.

`python3 tools/tuya_export.py` fetches the data from the Tuya Cloud (keys, `ble_unlock_check`, DP list).

Installation, `devices.json`, where to get the keys, arming mode and diagnostics:
**[docs/raykube-a1-ultra.en.md](docs/raykube-a1-ultra.en.md)**.

The fork uses the `tuya_local_ble` domain, so it replaces the original integration.
The other upstream devices work unchanged; they are described in [docs/UPSTREAM_README.md](docs/UPSTREAM_README.md).

### Tests

```bash
pip install bleak bleak-retry-connector pycryptodome pytest
pytest tests                    # tests that need HA run when homeassistant is installed
node tests/card/test_card.mjs   # card test in a browser (playwright + chromium)
```

### Credits

- **Jarosław Królikowski**: the Raykube A1 Ultra integration: lock settings, card, arming mode,
  command safety, diagnostics, connection modes, Tuya export.
- Based on [ShonP40/Tuya-BLE](https://github.com/ShonP40/Tuya-BLE) (Shon), which builds on
  [PlusPlus-ua/ha_tuya_ble](https://github.com/PlusPlus-ua/ha_tuya_ble): the Tuya BLE protocol layer and
  the other devices.

### License

MIT (see [LICENSE](LICENSE)). The license requires keeping the upstream copyright notice next to yours.
