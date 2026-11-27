[![ReadyStack](https://readystack.dev/logo.svg)](https://readystack.dev)

**Stable, Open-source Releases, Automated** · [readystack.dev](https://readystack.dev)

[![Docker Hub](https://img.shields.io/badge/Docker%20Hub-readystack%2Fflannel-2496ED?logo=docker&logoColor=white)](https://hub.docker.com/r/readystack/flannel) [![Docker pulls](https://img.shields.io/docker/pulls/readystack/flannel)](https://hub.docker.com/r/readystack/flannel) [![Grade at release](https://img.shields.io/badge/grade%20at%20release-A%20%2894%25%29-2ea44f)](https://readystack.dev/products/flannel) [![Signed: cosign](https://img.shields.io/badge/signed-cosign-2ea44f)](https://readystack.dev/keys/cosign.pub) ![SBOM: SPDX](https://img.shields.io/badge/SBOM-SPDX-2ea44f) ![Provenance: SLSA v1](https://img.shields.io/badge/provenance-SLSA%20v1-2ea44f) ![Offline rebuild: verified](https://img.shields.io/badge/offline%20rebuild-verified-2ea44f)

# flannel: ReadyStack build kit

The offline build kit for the Docker image `readystack/flannel:v0.28.10-CE-ubuntu24.04-r6`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v0.28.10-CE-ubuntu24.04-r6](https://github.com/readystack-dev/flannel/releases/tag/v0.28.10-CE-ubuntu24.04-r6)
- Image: [`docker.io/readystack/flannel:v0.28.10-CE-ubuntu24.04-r6`](https://hub.docker.com/r/readystack/flannel/tags), manifest digest `sha256:4b130f1f949e6e5a3dad42a9f534d5e0c031b8e56ba1b7a39e5d0287bd83c72a`
- Product page: https://readystack.dev/products/flannel
- Source and build recipe: https://readystack.dev/products/flannel/recipe
- Build trail: https://readystack.dev/products/flannel/trail/v0.28.10-CE-ubuntu24.04-r6
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=flannel

## The image

The kube-flannel DaemonSet (product-kits/kubevirt-stack/install/network/kube-flannel.yml, upstream's Documentation/kube-flannel.yml with our images and the cluster's pod CIDR): init containers install-cni-plugin (readystack/flannelcniplugin) and install-cni (this image, /opt/bin/install-conf), then flanneld --ip-masq --kube-subnet-mgr --healthz-port=8081 on the host network. The image's default user is the unprivileged flannel (uid 999); the pod sets runAsUser: 0 with NET_ADMIN and NET_RAW, because flanneld creates the VXLAN device, routes and iptables rules and writes /run/flannel on the host.

## Who uses flannel and why

Kubernetes cluster operators who need a simple network that connects pods across nodes.

flannel makes every pod's address reachable across the cluster. An agent on each host leases that host a subnet from a shared range and carries traffic between hosts, for example over VXLAN.

A typical use: A team building an on-premises Kubernetes cluster needs pods on different nodes to reach each other without port mapping. flannel runs on every node, gives each node a subnet of the pod address range, and forwards pod traffic between nodes over VXLAN.

From the upstream project's own description: https://github.com/flannel-io/flannel.

## This release

- Docker Scout policy grade at release: **A** (94%), graded 2026-11-27 with `docker scout policy` (CLI 1.26.0, docker-default configuration).
  - Docker Scout counts 106 packages with copyleft licences (GPL, LGPL, MPL), all of them operating-system packages (it counts only packages whose licence it can read; the SBOM lists every component).
- Signed with ReadyStack's release key (fingerprint `04daa92344a52dbc`): https://readystack.dev/keys/cosign.pub.
- Runs as a non-root user (`flannel`).
- Attestations on the image: an SPDX SBOM and SLSA provenance v1.
- Rebuilt from this kit with network egress blocked on 2026-11-27; the rebuilt image digest is the published one.
- Base: Ubuntu 24.04.

The current grade, measured again every day, is on https://readystack.dev/products/flannel.

flannel is distributed under its upstream licence (Apache-2.0); the kit contains upstream's source under that licence, and each vendored component under its own.

## Download

From the [v0.28.10-CE-ubuntu24.04-r6 release](https://github.com/readystack-dev/flannel/releases/tag/v0.28.10-CE-ubuntu24.04-r6): `ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r6.tar.gz` (440.3 MB), SHA-256 `68aa9e313cf24ef59ca6fb6e299f86ce7186564ae194f473a08c4e8e1e0e242e`.

```sh
sha256sum --check --ignore-missing SHA256SUMS
```

## Verify and rebuild

The rebuild statement is signed with the ReadyStack release key. Check the signature before you rely on it:

```sh
cosign verify-blob --key https://readystack.dev/keys/cosign.pub \
  --bundle ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r6.rebuild.json.sigstore ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r6.rebuild.json
```

Unpack with `-p` so file modes are kept, then follow the kit's own README.md and docs/REBUILD.md:

```sh
tar -xpzf ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r6.tar.gz
cd v0.28.10-CE-ubuntu24.04-r6
less README.md docs/REBUILD.md
```

The build trail (`ReadyStack-flannel-v0.28.10-CE-ubuntu24.04-r6.trail.json`, signed the same way, and its Markdown copy) records how this release was built and checked.

## This repository

`recipe/` holds the kit's recipe files (Dockerfiles, build scripts, pins) as published at https://readystack.dev/products/flannel/recipe. The complete kit is the release download. Each release is a tag here. How to report a vulnerability: [SECURITY.md](SECURITY.md).

## About ReadyStack

**Stable, Open-source Releases, Automated.** ReadyStack releases popular open-source apps every week with every available critical and high security fix applied, plus full documentation, reference data, tests and a build recipe that recreates each release bit-for-bit. Each release is rebuilt from its pinned inputs with the network off, checked against its published digest, signed, and scanned and graded again every day. The images and build kits are free, and ReadyStack is soon to be a nonprofit.

[readystack.dev](https://readystack.dev) · [All products](https://readystack.dev/products.html) · [Build kits on GitHub](https://github.com/readystack-dev) · [Defect log](https://readystack.dev/defects.html) · contact@readystack.dev

— ReadyStack · https://readystack.dev
