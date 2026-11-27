# =====================================================================
# L-009 HERMETIC OFFLINE BUILD (flannel v0.28.10) — the canonical,
# attestation-producing build (build_kit: buildx --network=none --attest ...).
# Follows upstream's images/Dockerfile at the pinned commit:
#   /opt/bin/flanneld        make dist/flanneld: CGO_ENABLED=1 on amd64 (the UDP
#                            backend's C proxy), -X pkg/version.Version=<tag>
#   /opt/bin/install-conf    CGO_ENABLED=0 go build ./cmd/install_conf/
#   /opt/bin/mk-docker-opts.sh  dist/mk-docker-opts.sh
#   /usr/sbin/iptables-wrapper  kubernetes-sigs/iptables-wrappers `make build`,
#                            then `iptables-wrapper install` (iptables legacy or
#                            nft picked at first use, matching the host)
#   ENTRYPOINT ["/opt/bin/flanneld"]
# Runtime userland from Ubuntu 24.04 packages in place of upstream's Alpine ones.
# flanneld links glibc dynamically (upstream links musl statically): it runs
# only inside this image, whose glibc it is built against.
# Default user is the unprivileged flannel (owner 2026-10-28); kube-flannel's
# pod gives root (runAsUser: 0, NET_ADMIN, NET_RAW), product-kits/kubevirt-stack/
# install/network/kube-flannel.yml.
# =====================================================================
FROM golang:1.26.11-bookworm@sha256:dc40a13b61304647ed7e5d581d246b86413dfc29d63c6d018310feb93cd98fc0 AS build_flanneld
WORKDIR /src
COPY vendored/flannel-src/ .
ENV CGO_ENABLED=1 GOTOOLCHAIN=local GOWORK=off GOFLAGS="-mod=vendor -buildvcs=false" GOPROXY=off SOURCE_DATE_EPOCH=1795672824
RUN go build -trimpath -ldflags='-s -w -X github.com/flannel-io/flannel/pkg/version.Version=v0.28.10' -o /flanneld . && \
    /flanneld --version > /tmp/flanneld.version 2>&1; cat /tmp/flanneld.version; \
    grep -qx 'v0.28.10' /tmp/flanneld.version || { echo "built flanneld does not report v0.28.10" >&2; exit 1; }
# Fail closed on a stale toolchain (the traefik chain-491 lesson): the binary records the Go it was built with.
RUN v=$(go version /flanneld | awk '{print $2}') && echo "flanneld built with $v" && \
    [ "$(printf '%s\n%s\n' go1.26.11 "$v" | sort -V | head -n1)" = go1.26.11 ] || \
    { echo "flanneld was built with '$v', older than the go1.26.11 floor: the golang base digest is stale" >&2; exit 1; }

FROM golang:1.26.11-bookworm@sha256:dc40a13b61304647ed7e5d581d246b86413dfc29d63c6d018310feb93cd98fc0 AS build_install_conf
WORKDIR /src
COPY vendored/flannel-src/ .
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOWORK=off GOFLAGS="-mod=vendor -buildvcs=false" GOPROXY=off SOURCE_DATE_EPOCH=1795672824
RUN go build -trimpath -o /install-conf ./cmd/install_conf/ && \
    { /install-conf 2>&1 | grep -q 'usage:'; } || { echo "built install-conf does not run" >&2; exit 1; }

FROM golang:1.26.11-bookworm@sha256:dc40a13b61304647ed7e5d581d246b86413dfc29d63c6d018310feb93cd98fc0 AS build_iptables_wrapper
WORKDIR /src
COPY vendored/iptables-wrappers-src/ .
# iptables-wrappers requires no module (go.mod has no require), so there is no vendor/ to read: -mod=mod with
# GOPROXY=off resolves nothing over the network and fails if a requirement ever appears.
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOWORK=off GOFLAGS="-mod=mod -buildvcs=false" GOPROXY=off SOURCE_DATE_EPOCH=1795672824
RUN ! grep -q '^require' go.mod && \
    go build -ldflags='-s -w -extldflags="-static" -buildid=""' -trimpath -o /iptables-wrapper github.com/kubernetes-sigs/iptables-wrappers

FROM ubuntu:24.04@sha256:9c7f200c07e3d4a03e7249d2c16bf66a51120fb876aa2242cf31451412543289
ENV DEBIAN_FRONTEND=noninteractive
# Reproducibility: /etc/shadow field 3 is days-since-epoch AT BUILD TIME, written by every RUN that adds a user: the
# dpkg RUN below (a strongSwan package's postinst adds the strongswan user) and the flannel user's RUN. A layer is the
# bytes its own RUN left, so each of those RUNs sees SOURCE_DATE_EPOCH (noble's useradd then stamps the epoch's day) and
# freezes the file itself; a freeze in a later RUN cannot change an earlier layer. r2 (live) ran the dpkg RUN before
# this ARG: its layer 2 holds strongswan:20764, its build day, under a SOURCE_DATE_EPOCH of day 20734 (the argocd r2
# finding, chain 981). ARG, never ENV.
ARG SOURCE_DATE_EPOCH=1795672824
# Offline apt: dpkg-install the vendored closure (CVE upgrades + runtime pkgs).
# --skip-same-version passes + configure-a; fails unless every vendored deb is ii at its version (L-A106).
# vendored/debs: bind-mounted (read-only), so never a layer of the image (kit migration vendored-bind-mount)
RUN --mount=type=bind,source=vendored/debs,target=/tmp/debs \
    set --; for f in /tmp/debs/*.deb; do [ -e "$f" ] && set -- "$@" "$f"; done; \
    for pass in 1 2 3 4 5; do [ $# -eq 0 ] || dpkg -i --skip-same-version "$@" >/dev/null 2>&1 && break; done; \
    dpkg --configure -a && bad= && for d in "$@"; do p=$(dpkg-deb -f "$d" Package) v=$(dpkg-deb -f "$d" Version); \
      case "$(dpkg-query -W -f='${Version} ${db:Status-Abbrev}' "$p" 2>/dev/null)" in "$v ii"*) ;; *) bad="$bad $p=$v" ;; esac; done && \
    { [ -z "$bad" ] || { echo "vendored debs not installed at their vendored version:$bad" >&2; false; }; } && \
    rm -rf /var/lib/apt/lists/* \
           /var/log/dpkg.log /var/log/alternatives.log /var/log/apt \
           /var/cache/ldconfig/aux-cache /var/cache/fontconfig && \
    : "${SOURCE_DATE_EPOCH:?SOURCE_DATE_EPOCH must be set for a reproducible build}" && \
    sed -ri "s/^([^:]*:[^:]*):[0-9]*:/\\1:$((SOURCE_DATE_EPOCH/86400)):/" /etc/shadow && \
    rm -f /etc/passwd- /etc/shadow- /etc/group- /etc/gshadow-
RUN groupadd -r flannel && useradd -r -g flannel -d /var/lib/flannel -m flannel && \
    : "${SOURCE_DATE_EPOCH:?SOURCE_DATE_EPOCH must be set for a reproducible build}" && \
    sed -ri "s/^([^:]*:[^:]*):[0-9]*:/\\1:$((SOURCE_DATE_EPOCH/86400)):/" /etc/shadow && \
    rm -f /etc/passwd- /etc/shadow- /etc/group- /etc/gshadow-
COPY --from=build_flanneld /flanneld /opt/bin/flanneld
COPY --from=build_install_conf /install-conf /opt/bin/install-conf
COPY vendored/flannel-src/dist/mk-docker-opts.sh /opt/bin/mk-docker-opts.sh
COPY --from=build_iptables_wrapper /iptables-wrapper /usr/sbin/iptables-wrapper
# images/Dockerfile: "check manually that iptables-legacy and iptables-nft are present", then
# `iptables-wrapper install` points iptables, ip6tables and their -save/-restore at the wrapper.
RUN chmod 755 /opt/bin/flanneld /opt/bin/install-conf /opt/bin/mk-docker-opts.sh /usr/sbin/iptables-wrapper && \
    which iptables-legacy && which iptables-nft && test -x /usr/lib/ipsec/charon && \
    test -f /usr/lib/ipsec/plugins/libstrongswan-vici.so && \
    /usr/sbin/iptables-wrapper install && \
    [ "$(readlink /usr/sbin/iptables)" = /usr/sbin/iptables-wrapper ] && \
    rm -f /var/log/alternatives.log
USER flannel
ENTRYPOINT ["/opt/bin/flanneld"]
# >>> readystack OCI labels: strategies/flannel.json image_labels plus the standard OCI labels,
# the set a generated kit carries (gen_dockerfile.label_lines). Written by scripts/kit_migrations.py;
# scripts/retarget.py re-renders it with every retarget. Never edit it by hand.
LABEL org.opencontainers.image.licenses="Apache-2.0"
LABEL org.opencontainers.image.title="flannel"
LABEL org.opencontainers.image.source="https://github.com/flannel-io/flannel"
LABEL org.opencontainers.image.version="v0.28.10-CE-ubuntu24.04-r6"
LABEL org.opencontainers.image.revision="0f70536f15f281279dab39fb9d575c646c7eea34"
# <<< readystack OCI labels
