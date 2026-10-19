# SenseLab Release

This repository stores Windows installers and update metadata for SenseLab.

## Latest version

- Version `v0.6`
- [Download SenseLabSetup_v0.6.exe](https://github.com/AlwhalesJointResearchTeam/CodeCrafter0308-AlwhalesJointResearchTeam-Senselab-Release/raw/main/SenseLabSetup_v0.6.exe)
- SHA-256 `FE4331489ADAFDC1219AFBD18BE7DE5920EE854DF51A5E743B4E5FCE12A6AB25`
- [Update metadata](./version.json)

This package updates the Windows application and supports board commands through 100 kHz. On 2026-10-19, the DielExct board completed calibration and passed the complete measurement and control test through both ZigBee and wired USB serial. The physical cause of the earlier AD5933 I2C failure remains unknown. Installing this application does not flash the STM32.

## V0.6 changes

- Uses ICECS text commands for fixed frequency, sweep start/end/step and heater PWM.
- Adds a DielExct-Main-MB-V1.1 profile with node 0014, 5 to 100 kHz frequency settings and configuration readback. Valid measurements require compatible firmware with successful calibration.
- Confirms the addressed node and requested settings before reporting success. DielExct configuration confirmation waits up to 15 seconds, with read-only query retries spaced 4 seconds apart and at most three queries in total. Incoming replies do not extend the deadline. Settings are never automatically resent. Legacy ICECS acknowledgement still uses a 5-second deadline.
- Handles delayed configuration replies and resumes parsing after damaged newline-delimited JSON.
- Automatically reads the selected single DielExct node's configuration when opening its serial port. The connection window distinguishes failed calibration, successful calibration awaiting samples, and active measurement reception. This query does not change board settings; ICECS and BLE connection behavior is unchanged.
- Keeps configuration JSON out of measurement plots and exported samples.
- Preserves the ICECS profile and loads old project files without sending commands.
- Defaults new projects to fixed 10 kHz, stored sweep 5 to 100 kHz in 1 kHz steps, and PWM 50%. Opening the app does not change the board.

## DielExct connection and commands

Use Serial Port for the USB ZigBee receiver, 38400 baud, 8 data bits, no parity and one stop bit. The board USB serial port uses 115200 baud with the same framing. Select the actual port enumerated by Windows. Select DielExct-Main-MB-V1.1 in Excitation Setting and enter sensor ID 0014, including leading zeros.

Opening the DielExct serial connection queries only the selected single node. If no valid single ID is selected, the application asks you to select one. Check the connection window for calibration and reception status. A board with failed calibration may reply with configuration JSON while sending no impedance measurement JSON. The Read Device Settings button remains available for manual queries.

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

97 automated tests passed. They cover protocol formatting, the 100 kHz input boundary, rejection above that limit, sweep settings, configuration confirmation, delayed replies, the 15-second confirmation deadline, 4-second query retries, recovery from damaged JSON, the single-node query on serial open, and calibration and measurement status messages.

At 17:08 on 2026-10-19, the current production firmware completed calibration after an MCU software restart. All 96 calibration points from 5 through 100 kHz were valid and configuration readback reported `calibrated=1`. The USB ZigBee receiver at 38400 baud received 35 complete measurement JSON messages in 12 seconds at fixed 10 kHz, plus one incomplete line at the start of capture. A 35-second wired USB observation also received continuous measurements and configuration with successful calibration.

The same board had previously reported `calibrated=0` during an AD5933 I2C failure and returned configuration without measurement samples. No production firmware change was made for this recovery. Its physical cause remains unknown, so successful reception in the latest check does not establish that the earlier fault has been permanently fixed. Temporary pin-tracing code has been removed; the production firmware retains its approximately 50 kHz I2C settings.

After calibration recovered, the current firmware and host code passed the complete measurement and control test over both wired USB serial and ZigBee. Each connection received fixed 20 and 100 kHz samples, all 96 frequencies from 5 through 100 kHz and restart at 5 kHz, cyclic 5/7/9 kHz, and repeated 8 kHz single-point measurements. PWM 30% and heating off were confirmed by configuration readback, and 101 kHz was rejected before transmission. Each test verified exact restoration to fixed 10 kHz, stored sweep 5 to 100 kHz in 1 kHz steps, and PWM 50%, with calibration still successful. Results are saved in the host project's `build/v06-100khz-validation/wired-recovery-measurements/results.json` and `build/v06-100khz-validation/zigbee-recovery-measurements/results.json`. PWM readback verifies the parameter, not physical heater voltage.

Some ZigBee commands did not take effect in an earlier test. The latest complete test passed, but long-term stability has not been validated. Older 40 kHz tests and tests on earlier 100 kHz firmware remain historical evidence only.

The latest firmware-side calibration record is `build/100khz-validation/calibration-latest-check.json`, with 96 valid points and `passed=true`. The firmware HEX SHA-256 is `80cb7dfa7eff013295c1a3c6f242111f7bb750391f7e47904a66b44aef804ff8`. Wireless reception is recorded in `zigbee-recovery-summary.json` in the same directory. These are fresh checks of the current board, not copies of historical successful results.

The local portable executable `dist/SenseLab_v0.6.exe` has SHA-256 `6C327E3B7F0650549FCCA669085067962E7CAAE30313AFEAC0A82BC593CCBAC2`.

Successful calibration, control and data transfer do not establish absolute impedance accuracy. The existing 51 kOhm feedback resistor and onboard RC may exceed the analog input range at high frequency; independent reference loads and waveform checks are still needed.

## Distribution

Older v0.1 through v0.5 installers remain available. Installer files use Git LFS. Download using the link above; a Git clone must fetch LFS content to obtain executable installers. The installer version is 0.6 and the Windows file version is 0.6.0.0.

This 100 kHz package replaces the initial 40 kHz V0.6 package. If V0.6 is already installed, download and reinstall this package; the version number remains 0.6.
