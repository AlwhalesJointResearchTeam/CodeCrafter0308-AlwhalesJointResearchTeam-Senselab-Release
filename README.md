# SenseLab Release

This repository stores Windows installers and update metadata for SenseLab.

## Latest version

- Version `v0.6`
- [Download SenseLabSetup_v0.6.exe](https://github.com/AlwhalesJointResearchTeam/CodeCrafter0308-AlwhalesJointResearchTeam-Senselab-Release/raw/main/SenseLabSetup_v0.6.exe)
- SHA-256 `4C911D5F1A1BD6DC6925875E1C7890252AE519FBB6B3F34C4A81BB6866180457`
- [Update metadata](./version.json)

## V0.6 changes

- Uses ICECS text commands for fixed frequency, sweep start/end/step and heater PWM.
- Adds a DielExct-Main-MB-V1.1 profile with node 0014, 5 to 40 kHz calibrated frequency limits and configuration readback.
- Confirms the addressed node and requested settings before reporting success. Missing query replies can trigger up to three read-only queries within five seconds. Settings are never automatically resent.
- Keeps configuration JSON out of measurement plots and exported samples.
- Preserves the ICECS profile and loads old project files without sending commands.
- Defaults new projects to fixed 10 kHz, stored sweep 5 to 40 kHz in 1 kHz steps, and PWM 50%. Opening the app does not change the board.

## DielExct connection and commands

Use Serial Port for the USB ZigBee receiver, 38400 baud, 8 data bits, no parity and one stop bit. The board USB serial port uses 115200 baud with the same framing. Select the actual port enumerated by Windows. Select DielExct-Main-MB-V1.1 in Excitation Setting and enter sensor ID 0014, including leading zeros.

The UI accepts Hz; the command protocol and received Freq use kHz. PWM is a ratio from 0 to 1, not a voltage or temperature measurement.

| Action | Command |
|---|---|
| Fixed 20 kHz | `set -o 1 -q 20 -id 0014` |
| Sweep 5, 7, 9 kHz | `set -o 0 -s 5 -e 9 -i 2 -id 0014` |
| PWM 30% | `set -pwm 0.3 -id 0014` |
| Heating off | `set -pwm 0 -id 0014` |
| Read settings | `get -id 0014` |

Newline terminates each command. Wait for completion before another operation. Device settings are held in RAM and reset to firmware defaults when the board restarts.

## Validation

67 automated tests passed. Production Qt serial transport was tested on a powered STM32L431CBT6 DielExct-Main-MB-V1.1 board over USB ZigBee and wired USB serial. Checks covered fixed 20 kHz, cyclic 5/7/9 kHz sweeps, single-point 8 kHz, PWM 30%, heating off, configuration readback and invalid-range rejection. Both tests restored fixed 10 kHz and PWM 50% afterward.

## Distribution

Older v0.1 through v0.5 installers remain available. Installer files use Git LFS. Download using the link above; a Git clone must fetch LFS content to obtain executable installers. The installer version is 0.6 and the Windows file version is 0.6.0.0.
