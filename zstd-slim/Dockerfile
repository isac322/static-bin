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
    && mkdir -p builddir /opt/release \
    && cmake \
      -DCMAKE_C_FLAGS='-Os -ffunction-sections -fdata-sections -static' \
      -DCMAKE_EXE_LINKER_FLAGS='-Wl,--gc-sections -s' \
      $(xx-clang --print-cmake-defines) \
      -DZSTD_BUILD_TESTS=OFF -DZSTD_BUILD_PROGRAMS=ON -DZSTD_BUILD_CONTRIB=OFF -DZSTD_PROGRAMS_LINK_SHARED=OFF \
      -DZSTD_BUILD_STATIC=ON -DZSTD_BUILD_SHARED=OFF \
      -DZSTD_MULTITHREAD_SUPPORT=ON -DZSTD_ZLIB_SUPPORT=OFF -DZSTD_LZMA_SUPPORT=OFF -DZSTD_LZ4_SUPPORT=OFF \
      -S build/cmake -B builddir --install-prefix /opt/release \
    && cmake --build builddir/programs --parallel "$(nproc)" --target install/strip \
    && cmake --install builddir/programs --strip --component install/strip \
    && xx-verify --static /opt/release/bin/zstd \
    && $(xx-info)-strip -s -R .comment -R .gnu.version --strip-unneeded /opt/release/bin/zstd \
    && if [ "${TARGETARCH}" != 's390x' ] && [ "${TARGETARCH}" != 'riscv64' ]; then \
         upx --ultra-brute --lzma /opt/release/bin/zstd; \
       fi \
    && mkdir -p /release \
    && echo "/release/$(xx-info march) /release/$(xx-info debian-arch)" | xargs -n 1 cp -vfp /opt/release/bin/zstd

FROM scratch as output
COPY --link --from=builder /release/ .

FROM scratch
ENTRYPOINT ["zstd"]
COPY --link --from=builder /opt/release/bin/ /usr/local/bin/
