# SenseLab Release

## Latest version

- Version `0.7.1`, released on 2026-10-28.
- [Download SenseLabSetup_v0.7.1.exe](https://github.com/AlwhalesJointResearchTeam/CodeCrafter0308-AlwhalesJointResearchTeam-Senselab-Release/raw/main/SenseLabSetup_v0.7.1.exe)
- SHA-256 `41DB33D251B8AC36C857BF31F3E78C3EAF576958267466609F2BA9F558B1EEE3`
- [Checksum file](./SenseLabSetup_v0.7.1.exe.sha256)
- [Update metadata](./version.json)

## Startup fix in 0.7.1

Version 0.7 could fail at launch with `DLL load failed while importing QtGui`.
The build picked up an unrelated Poppler ICU library from PATH. Its versioned
exports do not match the Windows ICU API required by Qt. Version 0.7.1 limits
dependency discovery to the Python installation and Windows, rejects bundled
ICU system libraries and API-set DLLs, and uses the target system's ICU.

Both normal and Windows-only PATH startup checks passed for the actual frozen
EXE on Windows 11 x64. They verify the main window, Windows Qt platform plugin,
TLS backend and loaded ICU path. 114 regression tests passed. The installer
embeds that exact tested executable. These checks did not connect to hardware.

## Unified excitation settings

- No board-model selection is needed. Updated nodes use the same addressed SET/GET protocol.
- Enter the target sensor ID, preserving leading zeros. Defaults are `0012`, 10 kHz fixed frequency and 50% PWM.
- Fixed and sweep frequencies range from 5 to 100 kHz. The UI accepts Hz and sends integer kHz.
- Settings are confirmed by configuration JSON from the target node, with bounded ZigBee retries.
- Existing projects retain their IDs and settings. Fragmented JSON and damaged replies are handled as in 0.7.

## Installation

Close the old app and run the 0.7.1 installer. This updates the Windows software
only. No firmware flash or manual DLL copying is needed. Application and installer
display version 0.7.1; the Windows installer file version is 0.7.1.0.

For a USB ZigBee receiver, select its actual COM port and 38400 baud, 8N1.
Opening the connection queries settings and does not change frequency or PWM.

## Distribution

Use the download link above. Installers are stored with Git LFS; a Git clone must
fetch LFS objects to obtain executables. Existing apps use version.json for updates.
Older packages remain for history. Version 0.7 is superseded by this startup fix.
