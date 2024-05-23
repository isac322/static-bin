# syntax=docker/dockerfile:1.5

FROM --platform=$BUILDPLATFORM tonistiigi/xx:1.4.0 AS xx
FROM --platform=$BUILDPLATFORM alpine:3.20 as builder
SHELL ["/bin/ash", "-eo", "pipefail", "-exc"]
# copy xx scripts to your build stage
COPY --from=xx / /
# export TARGETPLATFORM (or other TARGET*)
ARG TARGETPLATFORM
ARG TARGETARCH

ARG VERSION
WORKDIR /opt/curl

RUN wget -qO - https://curl.se/download/curl-${VERSION}.tar.xz | tar -Jxf - --strip-components=1 \
    && apk add --update autoconf automake libtool make clang llvm lld \
    && xx-apk add gcc musl-dev \
    && if [ "${TARGETARCH}" = 'ppc64le' ]; then export XX_CC_PREFER_LINKER=ld; fi \
    && xx-clang --setup-target-triple \
    && if echo 'int main() { return 0; }' | xx-clang -flto=thin -x c - 2>&1 /dev/null; then \
      CFLAGS_LTO='-flto=thin'; \
    fi \
    && autoreconf -fi \
    && CFLAGS="-Oz -ffunction-sections -fdata-sections -fno-unwind-tables -fno-asynchronous-unwind-tables ${CFLAGS_LTO}" \
       LDFLAGS="-Wl,-O2 -Wl,-s -Wl,-Bsymbolic -Wl,--gc-sections --static" \
       CC=xx-clang \
          ./configure \
          --host=$(xx-clang --print-target-triple) \
          --enable-static \
          --disable-shared \
          --disable-debug \
          --enable-optimize \
          --disable-curldebug \
          --disable-ares \
          --enable-shared=no \
          --disable-threaded-resolver \
          --enable-ipv6 \
          --without-zlib \
          --without-ssl \
          --disable-libcurl-option \
          --without-ca-bundle \
          --disable-rtsp \
          --disable-dict \
          --disable-file \
          --disable-ftp \
          --disable-gopher \
          --disable-imap \
          --disable-mqtt \
          --disable-pop3 \
          --disable-smtp \
          --disable-telnet \
          --disable-tftp \
    && make -j "$(nproc)" \
    && xx-verify --static src/curl \
    && $(xx-info triple)-strip -s -R .comment -R .gnu.version --strip-unneeded src/curl \
    && if [ "${TARGETARCH}" != 's390x' ] && [ "${TARGETARCH}" != 'riscv64' ]; then \
         apk add upx; \
         upx --ultra-brute --lzma src/curl; \
       fi \
    && mkdir -p /release \
    && echo "/release/$(xx-info march) /release/$(xx-info debian-arch)" | xargs -n 1 cp -vfp src/curl

FROM scratch as output
COPY --link --from=builder /release/ .

FROM scratch
ENTRYPOINT ["curl"]
COPY --link --from=builder /opt/curl/src/curl /usr/local/bin/curl
