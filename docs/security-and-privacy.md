# Blue Mouse security and privacy architecture

Blue Mouse is proprietary software. This document describes the current application and installer behavior at a high level; the complete source is not publicly distributed.

## What Blue Mouse does

Blue Mouse lets a physical mouse control OpenKneeboard while DCS World is the foreground application. It observes mouse movement and buttons through Windows Raw Input, can learn a selected HOTAS/HID button, and offers a middle-mouse-button toggle. It converts eligible mouse input into virtual-tablet input for OpenKneeboard. It also monitors whether DCS and OpenKneeboard are running and checks the foreground window so it can release Kneeboard Mouse Mode when DCS loses focus or the tablet client disconnects.

## Input access and Windows hooks

Blue Mouse registers for Windows Raw Input from mouse and relevant HID device classes, including joystick, gamepad, and multi-axis-controller usages. It reads HID button reports to learn and recognize the chosen HOTAS control; a device identifier/name and button number can be saved in local settings. Raw Input is registered with background delivery so the selected control can be observed while DCS is foreground.

A low-level mouse hook supports mouse suppression while Kneeboard Mouse Mode is active and handles the middle-button toggle. The application also installs a low-level keyboard hook for its legacy/test F8 activation path and Escape emergency release. F8 is not exposed as a production Settings choice. Blue Mouse needs these Windows capabilities to route input to OpenKneeboard while avoiding simultaneous mouse action in DCS. Input-monitoring and hook behavior may attract heuristic antivirus scrutiny; any detection should be investigated on its merits.

Mouse suppression is enabled when the mode is active, DCS is foreground, and an OpenKneeboard OTD-IPC client is connected. Toggle mode releases when those conditions stop being met. Escape requests emergency release. The hook is removed on normal application shutdown. These safeguards describe the current design; an abnormal process or operating-system failure may interrupt normal shutdown.

## DCS boundary

Blue Mouse detects the DCS process and uses the Windows foreground-window process to decide when input routing is eligible. The current application source contains no operation to modify DCS program files, Saved Games DCS files, or `Export.lua`; it does not install a DCS mod, use DCS scripting, inject code into DCS, or hook DCS internally. Its Windows input hooks belong to the Blue Mouse process and Windows input path, not to DCS code.

## OpenKneeboard and OTD-IPC

Blue Mouse presents a virtual tablet to OpenKneeboard using local OTD-IPC. The current implementation has a local named-pipe path and a local Unix-domain-socket path; neither is a remote Internet connection. The v2 path writes local discovery metadata and temporarily selects Blue Mouse as the local OTD-IPC default. It records the prior default and attempts to restore it on normal exit. A separate, hidden Blue Mouse recovery process waits for the main process to exit and can restore the previous default after a crash; a later launch also checks for stale recovery state. If another application changes the default meanwhile, the recovery path avoids overwriting that newer choice. Blue Mouse does not modify OpenKneeboard program files.

## Network behavior

The current application source contains no automatic Internet request, telemetry client, download function, or remote-server connection. OTD-IPC uses local interprocess communication. The About page offers support links; opening one is a user-initiated action that asks Windows to launch the selected URL in the user's browser. The `--diagnostics` view can copy a report to the clipboard at the user's request; the application does not automatically upload it.

## Local files and data

Settings are stored in `settings.json` beside the executable when that folder is writable, otherwise at `%LOCALAPPDATA%\BlueMouse\settings.json`. When possible, Blue Mouse can migrate an existing LocalAppData settings file to the writable install folder while retaining the original for recovery. Settings include the activation choice, optional HOTAS device identifier/name and button, mouse mapping and sensitivity, and window placement. They do not need to be published to use Blue Mouse.

Logs are written beside the executable in `logs\` when possible, otherwise under `%LOCALAPPDATA%\BlueMouse\logs\`. Logs can include device identifiers or names, process names, local paths, state changes, and errors. The v2 OTD-IPC path also uses local discovery, socket, and default-selection files under the user's local application data. Blue Mouse currently has no application registry read/write operation.

## Installation, privileges, and uninstall

The Blue Mouse installer requires administrator privileges for installation, including creating shortcuts and registering the uninstall entry, but Blue Mouse itself does not require administrator privileges during normal use. For reliable input handling, Blue Mouse, DCS, and OpenKneeboard should normally run at the same non-administrator privilege level. The installer records an installation log under the user's local application data. Uninstall preserves local Blue Mouse settings and data by default; removing that data requires two explicit confirmations. The installer has no download step in the current script.

This document describes the implementation reviewed for this draft. Check the documentation associated with a later release if its behavior changes.
