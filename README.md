# SenseLab Release

This repository stores Windows installers and update metadata for SenseLab.

## Latest version

- Version `0.7`, released on 2026-10-28.
- [Download SenseLabSetup_v0.7.exe](https://github.com/AlwhalesJointResearchTeam/CodeCrafter0308-AlwhalesJointResearchTeam-Senselab-Release/raw/main/SenseLabSetup_v0.7.exe)
- SHA-256 `72226654ED83EE0C840600F31218B0BB01B56DA3F7137E2482365CF69DEDEBD0`
- [Checksum file](./SenseLabSetup_v0.7.exe.sha256)
- [Update metadata](./version.json)

## Changes in 0.7

- Removes the board-model selector from excitation settings. All supported nodes use the same addressed SET/GET protocol.
- Select or enter the sensor ID, including leading zeros. The default is `0012`; use `0014` or the actual ID for other nodes.
- Supports fixed frequency and sweeps from 5 to 100 kHz, plus PWM duty from 0 to 1. The UI accepts frequency in Hz and sends integer kHz.
- Confirms settings using valid configuration JSON from the addressed node. Text acknowledgements alone do not indicate completion.
- Retries lost ZigBee settings and queries within the existing 15-second limit, with at most three SET and three GET attempts.
- Reassembles fragmented JSON and recovers after damaged newline-terminated messages.
- Loads existing project IDs and settings without restoring the old board selector or legacy confirmation mode.
- A `calibrated=0` flag is not automatically labelled a calibration failure because some boards use fixed calibration coefficients.

## Installation and use

Close the previous application and install this package. The installer includes the Windows x64 runtime and the application. The app and installer display version 0.7; the Windows installer file version is 0.7.0.0.

For the USB ZigBee receiver, choose its actual COM port and 38400 baud, 8 data bits, no parity and one stop bit. Enter the intended sensor ID in excitation settings. Opening a serial connection reads the selected node's settings. It does not change frequency or PWM.

This version expects firmware supporting addressed SET/GET configuration readback. The installer updates the Windows software only and does not flash a board.

## Validation

114 automated tests passed for the final 0.7 source. They cover command formatting, node isolation, settings confirmation, retry limits, fragmented/damaged replies and loading older projects. The Chinese frequency and PWM dialogs were visually checked. The packaged app version and absence of the board selector were checked in the executable, and the installer embeds that exact executable.

## Distribution

Older installers remain available. Installer files use Git LFS. Use the download link above; a Git clone must fetch LFS content to obtain executable installers. Existing applications check `version.json` for updates.
