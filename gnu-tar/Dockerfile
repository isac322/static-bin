# syntax=docker/dockerfile:1.19

FROM --platform=$BUILDPLATFORM tonistiigi/xx:1.8.0 AS xx
FROM --platform=$BUILDPLATFORM alpine:3.22 AS builder
SHELL ["/bin/ash", "-eo", "pipefail", "-exc"]
# copy xx scripts to your build stage
COPY --from=xx / /
# export TARGETPLATFORM (or other TARGET*)
ARG TARGETPLATFORM
ARG TARGETARCH

ARG VERSION
WORKDIR /opt/tar

RUN wget -qO - "https://ftp.gnu.org/gnu/tar/tar-${VERSION}.tar.xz" | tar -Jxf - --strip-components=1 \
    && apk add --update make clang llvm lld upx \
    && if [ "${TARGETARCH}" = 'ppc64le' ]; then export XX_CC_PREFER_LINKER=ld; fi \
    && xx-apk add gcc musl-dev \
    && xx-clang --setup-target-triple \
    && FORCE_UNSAFE_CONFIGURE=1 \
       CC=xx-clang \
       CFLAGS='-Os -ffunction-sections -fdata-sections -static -Wno-incompatible-function-pointer-types' \
       LDFLAGS='-Wl,--gc-sections -s -static' \
      ./configure --host=$(xx-clang --print-target-triple) \
    && make -j$(nproc) \
    && xx-verify --static src/tar \
    && $(xx-info)-strip -s -R .comment -R .gnu.version --strip-unneeded src/tar \
    && if [ "${TARGETARCH}" != 's390x' ] && [ "${TARGETARCH}" != 'riscv64' ]; then \
         upx --ultra-brute --lzma src/tar; \
       fi \
    && mkdir -p /release \
    && echo "/release/$(xx-info march) /release/$(xx-info debian-arch)" | xargs -n 1 cp -vfp src/tar

FROM scratch AS output
COPY --link --from=builder /release/ .

FROM scratch
ENTRYPOINT ["tar"]
COPY --link --from=builder /opt/tar/src/tar /usr/local/bin/tar
