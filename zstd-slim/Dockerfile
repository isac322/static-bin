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
    && xx-apk add gcc musl-dev \
    && xx-clang --setup-target-triple \
    && CC="$(xx-info)-clang" \
       CFLAGS='-Os -ffunction-sections -fdata-sections -DZSTD_LIB_DICTBUILDER=0 -DZSTD_LIB_DEPRECATED=0' \
       LDFLAGS='-Wl,--gc-sections -s' \
       make -j "$(nproc)" V=1 -C lib install-static-mt \
    && CC="$(xx-info)-clang" \
       CFLAGS='-Os -ffunction-sections -fdata-sections -static -DZSTD_NOBENCH -DZSTD_NODICT -DZSTD_NOTRACE -DZSTD_LEGACY_SUPPORT=0' \
       LDFLAGS='-Wl,--gc-sections -s -static' \
       make -j$(nproc) V=1 -C programs install prefix=/opt/release \
         HAVE_THREAD=1 HAVE_ZLIB=0 HAVE_LZMA=0 HAVE_LZ4=0 BACKTRACE=0 \
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
