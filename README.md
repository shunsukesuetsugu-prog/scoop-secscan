# scoop-secscan

[Scoop](https://scoop.sh) bucket for
[secscan](https://github.com/shunsukesuetsugu-prog/secscan) —
a multi-scanner security CLI on Windows.

## Install

```powershell
scoop bucket add secscan https://github.com/shunsukesuetsugu-prog/scoop-secscan
scoop install secscan
```

## Verify

```powershell
secscan --version           # → secscan 0.19.0
secscan --help              # lists all 11 subcommands
```

## What it ships

The `secscan` command — installed into an isolated venv under
`%USERPROFILE%\scoop\apps\secscan\<version>\venv` so the user's
global Python environment is untouched. The PyPI distribution
name is `shun-secscan` (the bare `secscan` was blocked by PyPI's
name-policy similarity gate against an unrelated package); only
the *distribution* name is `shun-secscan`, the CLI and Python
import name are both `secscan`.

## External tools (not bundled)

`secscan` orchestrates several external scanners. Install the
ones you need separately:

| Subcommand | External tool |
| ---------- | ------------- |
| `secrets`  | `scoop install gitleaks` |
| `deps`     | `scoop install nodejs` (npm / pnpm / yarn), pip-audit via Python extras |
| `sast`     | `pip install semgrep` (Linux/WSL recommended — semgrep on bare Windows is best-effort) |
| `dast`     | Docker Desktop + OWASP ZAP image (auto-pulled) |
| `config`   | Docker Desktop + Trivy image (auto-pulled) |
| `image`    | Docker Desktop + Trivy image (auto-pulled) |
| `sbom`     | Docker Desktop + Syft / Grype images (auto-pulled) |
| `apifuzz`  | Docker Desktop + Schemathesis image (auto-pulled) |
| `iast`     | `pip install pyrasp` in the operator's app environment |
| `supply`   | [cosign](https://github.com/sigstore/cosign/releases) — `scoop install cosign` if available, otherwise grab the release binary |

## Updating the manifest

After `shun-secscan` cuts a new PyPI release:

```powershell
scoop checkver secscan                    # confirms a newer version
# Manually edit bucket/secscan.json:
#   - "version"
#   - "url"  (copy from https://pypi.org/pypi/shun-secscan/<X.Y.Z>/json under .urls[bdist_wheel])
#   - "hash" (sha256: ... from the same JSON)
#   - the wheel filename in installer.script
git commit -am "secscan X.Y.Z"
git push
```

PyPI URLs contain a per-file hash prefix that Scoop's autoupdate
template substitution cannot reproduce, so the manifest does not
declare `autoupdate`. The manual three-line edit on each bump is
the explicit trade-off.
