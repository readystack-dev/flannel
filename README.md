# flannel: ReadyStack build kit

The offline build kit for the Docker image `readystack/flannel:v0.28.10-CE-ubuntu24.04-r5`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v0.28.10-CE-ubuntu24.04-r5](https://github.com/readystack-dev/flannel/releases/tag/v0.28.10-CE-ubuntu24.04-r5)
- Image: [`docker.io/readystack/flannel:v0.28.10-CE-ubuntu24.04-r5`](https://hub.docker.com/r/readystack/flannel/tags), manifest digest `sha256:75c7559e4aa18f44490fd82f8d8139108b66c5247ecae912e72ced996465340c`
- Product page: https://readystack.dev/products/flannel
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=flannel

flannel is distributed under its upstream licence (Apache-2.0); the kit contains upstream's source under that licence, and each vendored component under its own.

## Download

From the [v0.28.10-CE-ubuntu24.04-r5 release](https://github.com/readystack-dev/flannel/releases/tag/v0.28.10-CE-ubuntu24.04-r5): `ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r5.tar.gz` (440.3 MB), SHA-256 `d35079c732a49fc5017273ecde47d1133237ca5b631d0f6bf15860c41e23a1f9`.

```sh
sha256sum --check --ignore-missing SHA256SUMS
```

## Verify and rebuild

The rebuild statement is signed with the ReadyStack release key. Check the signature before you rely on it:

```sh
cosign verify-blob --key https://readystack.dev/keys/cosign.pub \
  --bundle ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r5.rebuild.json.sigstore ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r5.rebuild.json
```

Unpack with `-p` so file modes are kept, then follow the kit's own README.md and docs/REBUILD.md:

```sh
tar -xpzf ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r5.tar.gz
cd v0.28.10-CE-ubuntu24.04-r5
less README.md docs/REBUILD.md
```

The build trail (`ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r5.trail.json`, signed the same way, and its Markdown copy) records how this release was built and checked.

## This repository

`recipe/` holds the kit's recipe files (Dockerfiles, build scripts, pins) as published at https://readystack.dev/products/flannel/recipe. The complete kit is the release download. Each release is a tag here.
