# Blue Mouse

Blue Mouse provides OpenKneeboard mouse control for DCS. While DCS remains foreground, it converts physical mouse movement and left-clicks into OTD-IPC virtual-tablet input. A learned HOTAS button or middle mouse button can toggle between normal DCS mouse input and Kneeboard Mouse Mode.

## Status

The OTD-IPC v1 path, calibrated movement, pen-tip clicking, DCS mouse suppression, Escape release, learned HOTAS toggle, and single-instance protection have been confirmed in DCS/OpenKneeboard testing. Configuration persistence and the held-at-startup HOTAS edge guard still require final restart testing. The current OTD-IPC v2 path is protocol-tested but has not been confirmed with OpenKneeboard.

## Security & privacy

Blue Mouse is proprietary software; its complete source code is not publicly distributed. Starting with v1.0.3, official GitHub Releases include `SHA256SUMS.txt` so you can verify the downloaded installer's SHA-256 hash. See [SECURITY.md](SECURITY.md) for verification and security reporting, and [Security and privacy architecture](docs/security-and-privacy.md) for input, local IPC, network, and file behavior.

## Requirements

- Windows 10 or Windows 11, 64-bit
- DCS World
- OpenKneeboard (the confirmed environment used OpenKneeboard 1.12.10)
- .NET 10 Desktop Runtime

OpenTabletDriver must be stopped when its OTD-IPC server competes for OpenKneeboard's single tablet connection. Run Blue Mouse, DCS, and OpenKneeboard at the same non-administrator integrity level.

## Basic use

1. Start Blue Mouse, then OpenKneeboard and DCS.
2. In **Settings**, select **HOTAS Button**, choose **Bind**, and press the desired control. Blue Mouse assigns it automatically.
3. Confirm the Control page reports DCS running, OpenKneeboard running, and the tablet connected.
4. With DCS foreground, press the assigned HOTAS button once to enter **KNEEBOARD MOUSE** and again to return to **DCS MOUSE**.
5. Press **Escape** at any time for an emergency release.

Mouse Wheel/Center Click is also confirmed. Keyboard F8 remains available internally as a legacy/test source but is not exposed in production Settings. Only Toggle activation mode is currently implemented.

The confirmed mapping is:

- Swap X/Y: on
- Invert X: off
- Invert Y: on
- Sensitivity: 1.0

## Configuration

Blue Mouse selects its configuration path in this order:

1. `<Blue Mouse install folder>\settings.json`, when the executable directory passes a real write test.
2. `%LOCALAPPDATA%\BlueMouse\settings.json`, when the install directory is not writable.

If the LocalAppData settings file exists, the install-folder file does not, and the install folder is writable, Blue Mouse validates and atomically copies the existing settings to the install folder. It retains the source as recovery and never overwrites an existing install-folder configuration. Migration failure falls back to LocalAppData and is logged.

The active path appears under **Settings** and development Diagnostics. **Open Config Folder** opens it directly. Settings expose the production activation choice and HOTAS binding without changing the confirmed internal mapping.

## Diagnostics and logs

Launch with `--diagnostics` during development to expose the Diagnostics page. It shows process and foreground state, both OTD servers, client state, mouse deltas/buttons, virtual pen position, suppression/events, HOTAS state, active configuration, last error, and log path. **Copy Diagnostics** creates a concise clipboard report.

Logs normally live beside the executable in `logs`; if that folder is not writable, they fall back to `%LOCALAPPDATA%\BlueMouse\logs`. Use **Open Logs Folder** to locate the active folder.

Troubleshooting checks:

- If the tablet remains disconnected, fully stop OpenTabletDriver and restart OpenKneeboard after Blue Mouse.
- If activation does nothing, verify DCS is foreground and an OpenKneeboard client is connected.
- If input ever remains captured unexpectedly, press Escape and preserve the newest log.
- Do not run only one of Blue Mouse, DCS, or OpenKneeboard elevated.

## Installer and license

The installer presents the EULA, requests administrator privileges for installation, creates Start Menu and optional desktop shortcuts, and registers an uninstall entry. Uninstall preserves settings by default; optional data deletion requires confirmation. Blue Mouse itself should run at the same non-administrator integrity level as DCS and OpenKneeboard.

The executable, splash, About page, and installer use the approved Blue Mouse branding resources. About includes the approved Ko-fi, PayPal, and Patreon support URLs.
