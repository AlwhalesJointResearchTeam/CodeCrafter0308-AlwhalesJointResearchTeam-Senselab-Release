# SenseLab Release

This repository stores Windows installers and update metadata for SenseLab.

## Latest version

- Version `v0.6`
- [Download SenseLabSetup_v0.6.exe](https://github.com/AlwhalesJointResearchTeam/CodeCrafter0308-AlwhalesJointResearchTeam-Senselab-Release/raw/main/SenseLabSetup_v0.6.exe)
- SHA-256 `B4B8F41D050A2791345A2D6E808B054217A6CF246378159FFFF599D8CB8FA147`
- [Update metadata](./version.json)

This package updates the Windows application and supports board commands through 100 kHz. The current DielExct test board has an unresolved AD5933 calibration failure and reports `calibrated=0`. Final-firmware measurement validation has not passed. Some ZigBee setting commands also failed to take effect in the latest control test. Installing this application does not resolve that hardware/firmware issue or flash the STM32.

## V0.6 changes

- Uses ICECS text commands for fixed frequency, sweep start/end/step and heater PWM.
- Adds a DielExct-Main-MB-V1.1 profile with node 0014, 5 to 100 kHz frequency settings and configuration readback. Valid measurements require compatible firmware with successful calibration.
- Confirms the addressed node and requested settings before reporting success. DielExct configuration confirmation waits up to 15 seconds, with read-only query retries spaced 4 seconds apart and at most three queries in total. Incoming replies do not extend the deadline. Settings are never automatically resent. Legacy ICECS acknowledgement still uses a 5-second deadline.
- Handles delayed configuration replies and resumes parsing after damaged newline-delimited JSON.
- Keeps configuration JSON out of measurement plots and exported samples.
- Preserves the ICECS profile and loads old project files without sending commands.
- Defaults new projects to fixed 10 kHz, stored sweep 5 to 100 kHz in 1 kHz steps, and PWM 50%. Opening the app does not change the board.

## DielExct connection and commands

Use Serial Port for the USB ZigBee receiver, 38400 baud, 8 data bits, no parity and one stop bit. The board USB serial port uses 115200 baud with the same framing. Select the actual port enumerated by Windows. Select DielExct-Main-MB-V1.1 in Excitation Setting and enter sensor ID 0014, including leading zeros.

The UI accepts Hz; the command protocol and received Freq use kHz. PWM is a ratio from 0 to 1, not a voltage or temperature measurement.

| Action | Command |
|---|---|
| Fixed 20 kHz | `set -o 1 -q 20 -id 0014` |
| Fixed 100 kHz | `set -o 1 -q 100 -id 0014` |
| Sweep 5, 7, 9 kHz | `set -o 0 -s 5 -e 9 -i 2 -id 0014` |
| Sweep 5 to 100 kHz, step 1 kHz | `set -o 0 -s 5 -e 100 -i 1 -id 0014` |
| PWM 30% | `set -pwm 0.3 -id 0014` |
| Heating off | `set -pwm 0 -id 0014` |
| Read settings | `get -id 0014` |

Newline terminates each command. Wait for completion before another operation. Frequency settings and sweep endpoints must be in 1 kHz increments. The sweep span must be divisible by its step. Device settings are held in RAM and reset to firmware defaults when the board restarts. Intermittent ZigBee commands may not take effect even with the longer confirmation window. After a mismatch or timeout, read the actual settings before continuing. Verify restoration commands by readback as well.

## Validation

79 automated tests passed. They cover protocol formatting, the 100 kHz input boundary, rejection above that limit, sweep settings, configuration confirmation, delayed replies, the 15-second confirmation deadline, 4-second query retries and recovery from damaged JSON.

Earlier firmware completed fixed 100 kHz measurements and all 96 frequencies from 5 through 100 kHz, including restart at 5 kHz, on the STM32L431CBT6 test board. These are historical results from before the final single-point measurement change. One ZigBee run subsequently failed during later control checks. These records do not establish that the final firmware passes measurement validation.

The final production firmware was restored and its flash was verified. The latest calibration check on 2026-10-19 found zero valid calibration points and `calibrated=0`; the failure remained after complete board power removal and restart. At that check, configuration JSON was received through both USB ZigBee and wired USB serial, showing fixed 10 kHz, stored sweep 5 to 100 kHz in 1 kHz steps, and PWM 50%. Configuration readback confirms communication and stored settings, but does not confirm working impedance measurement. The AD5933 problem remains under investigation, and final measurement tests must be repeated after calibration succeeds.

Subsequent tests used the production Qt serial code to check parameters only. They did not validate measurement samples, calibration accuracy or physical heater voltage. The final 15-second confirmation version passed the complete control suite over wired USB serial and exactly restored fixed 10 kHz, stored sweep 5 to 100 kHz in 1 kHz steps, and PWM 50%. The board reported `calibrated=0` before and after that test.

The updated 15-second version confirmed fixed 20 and 100 kHz settings, full-sweep parameters and single-point sweep parameters over ZigBee. Its PWM 0.3 command did not take effect; readback remained 0.5. The application correctly reported a configuration mismatch and cancelled subsequent settings. The final command to restore fixed 10 kHz also failed to take effect, so that wireless test did not restore its initial settings. The later final wired test restored the settings successfully. The complete wireless control suite has not passed. Longer waiting accommodates delayed replies but does not resolve all wireless command failures.

The installer supports the host command protocol independently of the board's current calibration failure. It is not presented as a complete, validated 100 kHz measurement system. Even successful control and data-transfer tests do not establish absolute impedance accuracy. The existing 51 kOhm feedback resistor and onboard RC may exceed the analog input range at high frequency; independent reference loads and waveform checks are still needed.

## Distribution

Older v0.1 through v0.5 installers remain available. Installer files use Git LFS. Download using the link above; a Git clone must fetch LFS content to obtain executable installers. The installer version is 0.6 and the Windows file version is 0.6.0.0.

This 100 kHz package replaces the initial 40 kHz V0.6 package. If V0.6 is already installed, download and reinstall this package; the version number remains 0.6.
