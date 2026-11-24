# Changelog

## 0.6.0 - 2026-11-24

First release of the Heziis fork, based on upstream 0.5.3
([jaroslaw-krolikowski/raykube-a1-ultra-integration](https://github.com/jaroslaw-krolikowski/raykube-a1-ultra-integration) @ 4125ce1).

### Fixed

- Bounded startup wait: the initial BLE connect+setup during config-entry startup is wrapped in a
  90-second `asyncio.wait_for` bound. A lock that does not answer in time now fails cleanly with
  `ConfigEntryNotReady` and Home Assistant retries setup automatically, instead of leaving the
  config entry stuck half-loaded until a restart.
- Cancellation-safe error handling: bare `except:` clauses in `tuya_ble.py` replaced with
  `except Exception:`, so `asyncio.CancelledError` during shutdown/reload is no longer swallowed
  (it could corrupt connection state and break later reconnects).

### Changed

- Repository language cleanup: removed Polish-language README section, Polish docs and Polish UI
  translations; English versions remain.
- `manifest.json`: codeowners `@Heziis`; documentation and issue tracker URLs point at this fork.
- Added README installation section, this changelog, and GitHub issue templates.

Upstream credit: Jaroslaw Krolikowski (Raykube A1 Ultra integration), ShonP40/Tuya-BLE,
PlusPlus-ua/ha_tuya_ble. License: MIT.
