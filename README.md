# SenseLab Release

This repository stores public Windows installer packages and update metadata
for SenseLab.

## Latest Version

- Version: `v0.5`
- Installer: [`SenseLabSetup_v0.5.exe`](./SenseLabSetup_v0.5.exe)
- SHA-256: `BC752869BE1BCBE10A4471966C5CDEA4D8910BFD5DC36A1FD0CB4E4AE0003332`
- Update metadata: [`version.json`](./version.json)

## Files

- `SenseLabSetup_v0.1.exe` through `SenseLabSetup_v0.5.exe` are published
  installer packages.
- `version.json` describes the latest version, direct download URL, checksum,
  release notes, mandatory update flag, and patch list.
- `.gitattributes` keeps installer binaries in Git LFS.

## Usage

Download the latest installer from this repository or use the URL recorded in
`version.json`. When cloning this repository locally, make sure Git LFS is
installed so the installer files are downloaded correctly.

```powershell
git lfs install
git clone https://github.com/AlwhalesJointResearchTeam/CodeCrafter0308-AlwhalesJointResearchTeam-Senselab-Release.git
```

## Release Notes

`v0.5` adds per-channel excitation target selection, scan-mode labels in plot
legends, continued plotting after clearing curves, timestamp plotting for
channels with different frequencies, and hides abnormal negative temperature or
humidity readings.
