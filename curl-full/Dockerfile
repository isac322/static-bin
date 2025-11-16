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
WORKDIR /opt/curl

RUN wget -qO - https://curl.se/download/curl-${VERSION}.tar.xz | tar -Jxf - --strip-components=1 \
    && apk add --update cmake autoconf automake libtool make clang llvm lld pkgconfig groff \
    && xx-apk add gcc musl-dev \
      c-ares-dev \
      openssl-dev openssl-libs-static \
      zlib-static zlib-dev \
      libssh2-dev libssh2-static \
      zstd-dev zstd-static \
      nghttp2-dev nghttp2-static \
      nghttp3-dev nghttp3-static \
      libidn2-dev libidn2-static \
    && if [ "${TARGETARCH}" = 'ppc64le' ]; then export XX_CC_PREFER_LINKER=ld; fi \
    && xx-clang --setup-target-triple \
    && if echo 'int main() { return 0; }' | xx-clang -flto=thin -x c - 2>&1 /dev/null; then \
      CFLAGS_LTO='-flto=thin'; \
    fi \
    && mkdir -p /opt/brotli/out \
    && wget -qO - https://github.com/google/brotli/archive/refs/tags/v1.1.0.tar.gz | tar -zxf - --strip-components=1 -C /opt/brotli \
    && cmake \
      $(xx-clang --print-cmake-defines) \
      -DCMAKE_C_FLAGS="-Oz -ffunction-sections -fdata-sections -fno-unwind-tables -fno-asynchronous-unwind-tables ${CFLAGS_LTO}" \
      -DCMAKE_EXE_LINKER_FLAGS="-Wl,-O2 -Wl,-s -Wl,-Bsymbolic -Wl,--gc-sections" \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_INSTALL_PREFIX="$(xx-info sysroot)usr" \
      -DBUILD_SHARED_LIBS=OFF \
      -DBUILD_TESTING=OFF \
      -DBROTLI_DISABLE_TESTS=ON \
      -S /opt/brotli -B /opt/brotli/out \
    && cmake --build /opt/brotli/out --parallel "$(nproc)" --target install \
    && autoreconf -fi \
    && CFLAGS="-Oz -ffunction-sections -fdata-sections -fno-unwind-tables -fno-asynchronous-unwind-tables ${CFLAGS_LTO}" \
       LDFLAGS="-Wl,-O2 -Wl,-s -Wl,-Bsymbolic -Wl,--gc-sections -lbrotlicommon --static" \
       CC=xx-clang \
          ./configure \
          --host=$(xx-clang --print-target-triple) \
          --enable-static \
          --disable-shared \
          --disable-debug \
          --enable-optimize \
          --disable-curldebug \
          --enable-ares \
          --enable-shared=no \
          --with-libssh2 \
          --with-brotli \
          --with-zstd \
          --enable-ipv6 \
          --with-libidn2 \
          --enable-manual \
          --disable-threaded-resolver \
          --with-nghttp2 \
          --with-nghttp3 \
          --enable-websockets \
          --with-openssl \
          --with-openssl-quic \
          --with-pic \
          --without-libpsl \
    && make -j "$(nproc)" \
    && xx-verify --static src/curl \
    && $(xx-info triple)-strip -s -R .comment -R .gnu.version --strip-unneeded src/curl \
    && if [ "${TARGETARCH}" != 's390x' ] && [ "${TARGETARCH}" != 'riscv64' ]; then \
         apk add upx; \
         upx --ultra-brute --lzma src/curl; \
       fi \
    && mkdir -p /release \
    && echo "/release/$(xx-info march) /release/$(xx-info debian-arch)" | xargs -n 1 cp -vfp src/curl

FROM scratch AS output
COPY --link --from=builder /release/ .

FROM scratch
ENTRYPOINT ["curl"]
COPY --link --from=builder /opt/curl/src/curl /usr/local/bin/curl
