# syntax=docker/dockerfile:1.5

FROM --platform=$BUILDPLATFORM tonistiigi/xx AS xx
FROM --platform=$BUILDPLATFORM alpine as builder
SHELL ["/bin/ash", "-eo", "pipefail", "-c"]
# copy xx scripts to your build stage
COPY --from=xx / /
# export TARGETPLATFORM (or other TARGET*)
ARG TARGETPLATFORM
ARG TARGETARCH

ARG VERSION
WORKDIR /opt/curl

RUN wget -O- https://curl.se/download/curl-${VERSION}.tar.xz | tar -Jxf - -C /opt/curl --strip-components=1 \
    && apk add --update cmake make clang llvm lld upx \
    && if [ "${TARGETARCH}" = 'ppc64le' ]; then export XX_CC_PREFER_LINKER=ld; fi \
    && xx-apk add gcc musl-dev \
    && xx-clang --setup-target-triple \
    && mkdir -p builddir /opt/release \
    && cmake \
      -DCMAKE_C_FLAGS='-Os -ffunction-sections -fdata-sections' \
      -DCMAKE_EXE_LINKER_FLAGS='-Wl,--gc-sections -s -static' \
      $(xx-clang --print-cmake-defines) \
      -DBUILD_SHARED_LIBS=OFF -DBUILD_STATIC_LIBS=OFF -DBUILD_STATIC_CURL=ON -DENABLE_ARES=OFF \
      -DENABLE_UNICODE=ON \
      -DCURL_ENABLE_SSL=OFF -DHTTP_ONLY=ON \
      -S . -B builddir --install-prefix /opt/release \
    && cmake --build builddir --parallel "$(nproc)"  \
    && $(xx-info triple)-strip -s -R .comment -R .gnu.version --strip-unneeded builddir/src/curl \
    && xx-verify --static builddir/src/curl \
    && if [ "${TARGETARCH}" != 's390x' ] && [ "${TARGETARCH}" != 'riscv64' ]; then \
         apk add upx; \
         upx --ultra-brute --lzma builddir/src/curl; \
       fi \
    && mkdir -p /release \
    && echo "/release/$(xx-info march) /release/$(xx-info debian-arch)" | xargs -n 1 cp -vfp builddir/src/curl

FROM scratch as output
COPY --link --from=builder /release/ .

FROM scratch
ENTRYPOINT ["curl"]
COPY --link --from=builder /opt/curl/builddir/src/curl /usr/local/bin/curl
