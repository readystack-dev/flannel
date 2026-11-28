# Provenance — flannel (`v0.28.11-CE-ubuntu24.04-r1`)

Every item below is copied from a record in this archive; the record is named in brackets.

## Source

- Upstream repository: https://github.com/flannel-io/flannel
- Revision built: `v0.28.11` (commit `6fe2579f6588feaaf44fc9f576c3f66af2c2e822`)
- Source tree: `bbe66e8e8707707772a4adfd0736949bd0a08956e8fc55fadf84c9ff7fd05e33` over 233 files [`docs/provenance/SOURCE_TREE_HASH.json`]
- Community Edition only: enterprise code is removed or refused before the build (CE audit at acquire).

## Build

- Method: `source-rebuild-go`; base image `ubuntu:24.04`
- Compiled with `--network=none` from vendored source, dependencies and base images; BuildKit pinned to `moby/buildkit:v0.31.2` (shipped in `toolchain/`).
- SOURCE_DATE_EPOCH: `1795849894` [`SOURCE_DATE_EPOCH.txt`]
- Frozen inputs: [`docs/provenance/FREEZE_MANIFEST.json`], build recipe: [`docs/provenance/BUILD_RECIPE.json`].

## Output

- Published image: `readystack/flannel:v0.28.11-CE-ubuntu24.04-r1` — index digest `sha256:f0119a279f1e041d15a15b7abb0372275320989100efd603a6739e381e8beb72` [`docs/provenance/LOCKED_DIGEST.txt`]
- linux/amd64 image digest `sha256:de778b27a17892ff10757cbf40500ccb3b33f9deb54334ced57741d3a210caa0` — reproduced from this archive before publishing [`docs/provenance/REPRO_VERIFICATION.json`].

## Attestations and signature

- SBOM + SLSA provenance attached to the image: attached: an SPDX SBOM and SLSA v1 provenance [`docs/provenance/SCOUT_VERDICT.json`]
- cosign signature by the ReadyStack release key: verified before publishing. Check it: `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/flannel:v0.28.11-CE-ubuntu24.04-r1`

## Pinned inputs, and why

- 18 pinned inputs, each with where it came from, why this one, how it was checked and when it was decided: `docs/PINS.md` [`docs/provenance/PINS.json`] (upstream source 2, base and build images 3, image digests 2, dependency versions readystack sets 2, components from other repositories 1, pins in the kit's dockerfiles 5, readystack's build engine 1, dependencies upstream chose 2).

## Build trail

- How this release passed through the ReadyStack harness, record by record (every chain, drive, stage, review round, defect and check, each naming the record it is copied from): `ReadyStack-flannel-v0.28.11-CE-ubuntu24.04-r1.trail.json`, published beside this archive and signed with the release key (`ReadyStack-flannel-v0.28.11-CE-ubuntu24.04-r1.trail.json.sigstore`), and readable at https://readystack.dev/products/flannel/trail/v0.28.11-CE-ubuntu24.04-r1.

