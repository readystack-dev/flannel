# flannel v0.28.10 — ReadyStack release `v0.28.10-CE-ubuntu24.04-r5`

Generated 2026-11-20 from this release's build records and strategy. Statements that come from a single record name it in brackets; the others come from this release's strategy and build log.

## Release

- **Image:** `readystack/flannel:v0.28.10-CE-ubuntu24.04-r5` (linux/amd64). Index digest `sha256:45b7c97dae65dd8acf6806f47d0ff2a36e0e371ce4fa69cc427dac90b2b134b1` [`docs/provenance/LOCKED_DIGEST.txt`].
- **Image manifest digest:** `sha256:75c7559e4aa18f44490fd82f8d8139108b66c5247ecae912e72ced996465340c` [`TARGET_DIGEST.txt`].
- **Upstream:** flannel v0.28.10 built from source at `v0.28.10` of https://github.com/flannel-io/flannel — upstream notes: https://github.com/flannel-io/flannel/releases/tag/v0.28.10 [`docs/provenance/BUILD_RECIPE.json`].
- **Base image:** `ubuntu:24.04`; runtime user `flannel`.
- **Build:** `source-rebuild-go`, compiled offline with `--network=none`.
- **Supersedes:** ReadyStack release `v0.28.10-CE-ubuntu24.04-r4`.

## What changed in this release

- Upstream version unchanged (v0.28.10); ReadyStack rebuild.
- Minimum dependency versions ReadyStack enforces on top of upstream (raised only where upstream's own version was lower); shipped versions from the SBOM [`docs/sbom/SBOM.json`]:
  - `golang.org/x/net` raised from upstream's `v0.58.0` to at least `v0.60.0` (shipped: `v0.62.0`)
  - `golang.org/x/crypto` raised from upstream's `v0.55.0` to at least `v0.56.0` (shipped: `v0.58.0`)
- flannel v0.28.10 is unchanged from the release this supersedes (v0.28.10-CE-ubuntu24.04-r4); this release rebuilds it. Changes: the image is rebuilt on the current ubuntu:24.04 image (sha256:9c7f200c07e3…), which its maintainers updated after v0.28.10-CE-ubuntu24.04-r4 was built on sha256:2198e4c65076…. Settings, ports and data volumes are those of v0.28.10-CE-ubuntu24.04-r4: replace the container with this image on the same volumes.

## Security status at release (measured 2026-11-20)

- **Docker Scout:** 0 fixable critical/high vulnerabilities; runs as `flannel` (non-root); SBOM + provenance attestations present (SPDX SBOM + SLSA v1) [`docs/provenance/SCOUT_VERDICT.json`].
- **OSV cross-check:** no fixable critical/high vulnerability beyond Docker Scout's findings.
- **Signature:** cosign signature verified by ReadyStack's release gate before publishing; check it yourself with `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/flannel:v0.28.10-CE-ubuntu24.04-r5` (signatures are recorded in the public Sigstore transparency log).
- **ReadyStack release check at release:** passed (ReadyStack's own test, not a Docker Scout grade). Vulnerabilities are disclosed every day and the image never changes, so it is re-checked daily, beside Docker Scout's own dated grade, at https://readystack.dev/queue.

## Reproducibility

Rebuilds offline from this archive to the identical image digest `sha256:75c7559e4aa18f44490fd82f8d8139108b66c5247ecae912e72ced996465340c`: run `./BUILD_OFFLINE.sh`, then `./VERIFY_OFFLINE.sh` (add `--online` to confirm the Docker Hub tag holds the same digest). Before publishing, ReadyStack rebuilt the kit with `./BUILD_OFFLINE.sh` and compared the result with the published digest [`docs/provenance/REPRO_VERIFICATION.json`], then ran `./BUILD_OFFLINE.sh` and `./VERIFY_OFFLINE.sh` from this archive (without `--online`).

## Validation

- 5 of 5 runbook checks passed (5 authored) on the stack's 3-node integration cluster, recorded 2026-10-29T02:34:55Z [`docs/validation/test-results.json`, from the run's record `docs/stack/RUNBOOK_EVIDENCE.json`]. They are the checks of the stack's operator runbook that name this component; no container of this image was started on its own for them. The record does not show this release's image (`sha256:75c7559e4aa18f44490fd82f8d8139108b66c5247ecae912e72ced996465340c`) in that run: it lists the component's images as they were then, an earlier build or a tag, so the checks describe the component, not this digest.
- Failed cases are listed in `docs/validation/FAILED_TESTS.md`.

## Known issues

- None found (reviewed by held semantic review (Claude) on 2026-11-20).

## Verify this archive

```
sha256sum -c CHECKSUMS.sha256
./BUILD_OFFLINE.sh && ./VERIFY_OFFLINE.sh --online
cosign verify --key https://readystack.dev/keys/cosign.pub readystack/flannel:v0.28.10-CE-ubuntu24.04-r5
```

ReadyStack records every signature in the public Sigstore transparency log (Rekor), so the command needs no extra flags.

