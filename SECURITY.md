# Blue Mouse security

Blue Mouse is proprietary software. Its complete source code is maintained privately and is not publicly distributed. Official installers are distributed through the GitHub Releases page of [bluexone116/BlueMouse](https://github.com/bluexone116/BlueMouse). Public documentation describes security and privacy behavior; it is not a substitute for the complete source or a reproducible build.

## Verify a release download

Download the installer and `SHA256SUMS.txt` from the same official release. In PowerShell, run:

```powershell
Get-FileHash -Algorithm SHA256 -LiteralPath .\BlueMouse-x.x.x-Setup.exe
```

Compare the complete hash with the hash on the corresponding line of `SHA256SUMS.txt`, and confirm that the filename on that line is the installer you downloaded. A match checks file integrity against the published checksum; it does not by itself establish publisher identity. Do not use a download if the values differ.

## Report a suspected security problem

Report suspected security vulnerabilities privately through [bluexone116/BlueMouse](https://github.com/bluexone116/BlueMouse): open the repository's **Security and quality** tab, then select **Report a vulnerability** and submit the private report. GitHub may place this option under **Advisories** or in a tab menu, depending on the interface.

Do **not** report security vulnerabilities as public GitHub Issues. Describe the Blue Mouse version, what happened, and steps to reproduce it if safe to share. Do not include passwords, tokens, credentials, private DCS data, or unnecessary sensitive personal information. Review logs before sharing them because they may contain device names, process names, and local paths.

For a high-level description of input access, local files, and OpenKneeboard communication, see [Security and privacy architecture](docs/security-and-privacy.md).
