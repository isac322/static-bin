# syntax=docker/dockerfile:1.5

FROM --platform=$BUILDPLATFORM tonistiigi/xx AS xx
FROM --platform=$BUILDPLATFORM alpine as builder
SHELL ["/bin/ash", "-eo", "pipefail", "-c"]
# copy xx scripts to your build stage
COPY --from=xx / /
# export TARGETPLATFORM (or other TARGET*)
ARG TARGETPLATFORM

ARG VERSION
WORKDIR /opt/tar

RUN wget -qO - "https://ftp.gnu.org/gnu/tar/tar-${VERSION}.tar.xz" | tar -Jxf - --strip-components=1 \
    && apk add --update make clang lld ccache upx \
    && xx-apk add gcc musl-dev \
    && xx-clang --wrap \
    && FORCE_UNSAFE_CONFIGURE=1 CC='ccache clang' CFLAGS="-static -Wno-incompatible-function-pointer-types" \
      ./configure --host=$(xx-clang --print-target-triple) \
    && make -j$(nproc) \
    && xx-verify --static src/tar \
    && upx --ultra-brute src/tar

FROM scratch as output
ARG TARGETARCH
COPY --link --from=builder /opt/tar/src/tar tar-$TARGETARCH

FROM scratch
ENTRYPOINT ["tar"]
COPY --link --from=builder /opt/tar/src/tar /usr/local/bin/tar
