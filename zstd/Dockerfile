FROM --platform=$BUILDPLATFORM tonistiigi/xx AS xx
FROM --platform=$BUILDPLATFORM alpine as builder
SHELL ["/bin/ash", "-eo", "pipefail", "-c"]
# copy xx scripts to your build stage
COPY --from=xx / /
# export TARGETPLATFORM (or other TARGET*)
ARG TARGETPLATFORM
ARG TARGETARCH

ARG VERSION
WORKDIR /opt/zstd

RUN wget -qO- "https://github.com/facebook/zstd/releases/download/v${VERSION}/zstd-${VERSION}.tar.gz" \
      | tar -zxf - --strip-components=1 \
    && apk add --update cmake make clang llvm lld upx \
    && if [ "${TARGETARCH}" = 'ppc64le' ]; then export XX_CC_PREFER_LINKER=ld; fi \
    && xx-apk add gcc g++ musl-dev \
    && xx-clang --setup-target-triple \
    && mkdir builddir \
    && cmake \
      -DCMAKE_C_FLAGS='-Os -ffunction-sections -fdata-sections -static' \
      -DCMAKE_EXE_LINKER_FLAGS='-Wl,--gc-sections,-s' \
      $(xx-clang --print-cmake-defines) \
      -DZSTD_BUILD_TESTS=OFF -DZSTD_BUILD_PROGRAMS=ON -DZSTD_BUILD_CONTRIB=OFF -DZSTD_PROGRAMS_LINK_SHARED=OFF \
      -DZSTD_BUILD_STATIC=ON -DZSTD_BUILD_SHARED=OFF \
      -DZSTD_MULTITHREAD_SUPPORT=ON -DZSTD_ZLIB_SUPPORT=OFF -DZSTD_LZMA_SUPPORT=OFF -DZSTD_LZ4_SUPPORT=OFF \
      -S build/cmake -B builddir \
    && make -C builddir -j$(nproc) \
    && xx-verify --static builddir/programs/zstd \
    && $(xx-info)-strip -s -R .comment -R .gnu.version --strip-unneeded builddir/programs/zstd \
    && if [ "${TARGETARCH}" != 's390x' ] && [ "${TARGETARCH}" != 'riscv64' ]; then \
         upx --ultra-brute builddir/programs/zstd; \
       fi \
    && mkdir -p /release \
    && echo "/release/$(xx-info march) /release/$(xx-info debian-arch)" | xargs -n 1 cp -vfp builddir/programs/zstd

FROM scratch as output
COPY --link --from=builder /release/ .

FROM ghcr.io/isac322/gnu-tar:1.32 as tar
FROM scratch
ENTRYPOINT ["zstd"]
COPY --link --from=tar /usr/local/bin/tar /usr/local/bin/tar
COPY --link --from=builder /opt/zstd/builddir/programs/zstd /usr/local/bin/zstd
