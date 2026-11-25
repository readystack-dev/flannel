# flannel: ReadyStack build kit

The offline build kit for the Docker image `readystack/flannel:v0.28.10-CE-ubuntu24.04-r5`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v0.28.10-CE-ubuntu24.04-r5](https://github.com/readystack-dev/flannel/releases/tag/v0.28.10-CE-ubuntu24.04-r5)
- Image: [`docker.io/readystack/flannel:v0.28.10-CE-ubuntu24.04-r5`](https://hub.docker.com/r/readystack/flannel/tags), manifest digest `sha256:75c7559e4aa18f44490fd82f8d8139108b66c5247ecae912e72ced996465340c`
- Product page: https://readystack.dev/products/flannel
- Source and build recipe: https://readystack.dev/products/flannel/recipe
- Build trail: https://readystack.dev/products/flannel/trail/v0.28.10-CE-ubuntu24.04-r5
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=flannel

## The image

The kube-flannel DaemonSet (product-kits/kubevirt-stack/install/network/kube-flannel.yml, upstream's Documentation/kube-flannel.yml with our images and the cluster's pod CIDR): init containers install-cni-plugin (readystack/flannelcniplugin) and install-cni (this image, /opt/bin/install-conf), then flanneld --ip-masq --kube-subnet-mgr --healthz-port=8081 on the host network. The image's default user is the unprivileged flannel (uid 999); the pod sets runAsUser: 0 with NET_ADMIN and NET_RAW, because flanneld creates the VXLAN device, routes and iptables rules and writes /run/flannel on the host.

## Who uses flannel and why

Kubernetes cluster operators who need a simple network that connects pods across nodes.

flannel makes every pod's address reachable across the cluster. An agent on each host leases that host a subnet from a shared range and carries traffic between hosts, for example over VXLAN.

A typical use: A team building an on-premises Kubernetes cluster needs pods on different nodes to reach each other without port mapping. flannel runs on every node, gives each node a subnet of the pod address range, and forwards pod traffic between nodes over VXLAN.

From the upstream project's own description: https://github.com/flannel-io/flannel.

## This release

- Docker Scout policy grade at release: **A** (94%), graded 2026-11-20 with `docker scout policy` (CLI 1.26.0, docker-default configuration).
  - Docker Scout counts 106 packages with copyleft licences (GPL, LGPL, MPL), all of them operating-system packages (it counts only packages whose licence it can read; the SBOM lists every component).
- Signed with ReadyStack's release key (fingerprint `04daa92344a52dbc`): https://readystack.dev/keys/cosign.pub.
- Runs as a non-root user (`flannel`).
- Attestations on the image: an SPDX SBOM and SLSA provenance v1.
- Rebuilt from this kit with network egress blocked on 2026-11-20; the rebuilt image digest is the published one.
- Base: Ubuntu 24.04.

The current grade, measured again every day, is on https://readystack.dev/products/flannel.

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

— ReadyStack · https://readystack.dev
