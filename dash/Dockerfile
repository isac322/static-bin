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
WORKDIR /opt/dash

RUN wget -qO - "https://git.kernel.org/pub/scm/utils/dash/dash.git/snapshot/dash-${VERSION}.tar.gz" | tar -zxf - --strip-components=1 \
    && apk add --update automake autoconf make clang llvm lld upx \
    && if [ "${TARGETARCH}" = 'ppc64le' ]; then export XX_CC_PREFER_LINKER=ld; fi \
    && xx-apk add gcc musl-dev \
    && xx-clang --setup-target-triple \
    && autoreconf -fiv \
    && CC=xx-clang \
       CFLAGS='-Os -ffunction-sections -fdata-sections' \
       LDFLAGS='-Wl,--gc-sections -s' \
      ./configure --host=$(xx-clang --print-target-triple) --enable-static \
    && make -j$(nproc) \
    && xx-verify --static src/dash \
    && $(xx-info)-strip -s -R .comment -R .gnu.version --strip-unneeded src/dash \
    && if [ "${TARGETARCH}" != 's390x' ] && [ "${TARGETARCH}" != 'riscv64' ]; then \
         upx --ultra-brute --lzma src/dash; \
       fi \
    && mkdir -p /release \
    && echo "/release/$(xx-info march) /release/$(xx-info debian-arch)" | xargs -n 1 cp -vfp src/dash

FROM scratch as output
COPY --link --from=builder /release/ .

FROM scratch
ENTRYPOINT ["dash"]
COPY --link --from=builder /opt/dash/src/dash /usr/local/bin/dash
