# syntax=docker/dockerfile:1.5

FROM tonistiigi/xx:1.3.0 AS xx
FROM alpine:3.19 as builder
SHELL ["/bin/ash", "-eo", "pipefail", "-exc"]
# copy xx scripts to your build stage
COPY --from=xx / /
# export TARGETPLATFORM (or other TARGET*)
ARG TARGETPLATFORM
ARG TARGETARCH

ARG VERSION
WORKDIR /opt/dash

RUN wget -qO - "https://git.kernel.org/pub/scm/utils/dash/dash.git/snapshot/dash-${VERSION}.tar.gz" | tar -zxf - --strip-components=1 \
    && apk add --update automake autoconf make gcc musl-dev \
    && ./autogen.sh \
    && CFLAGS='-Os -ffunction-sections -fdata-sections' \
       LDFLAGS='-Wl,--gc-sections -s' \
      ./configure --enable-static \
    && make -j$(nproc) \
    && strip -s -R .comment -R .gnu.version --strip-unneeded src/dash \
    && if [ "${TARGETARCH}" != 's390x' ] && [ "${TARGETARCH}" != 'riscv64' ]; then \
         apk add upx; \
         upx --ultra-brute --lzma src/dash; \
       fi \
    && mkdir -p /release \
    && echo "/release/$(xx-info march) /release/$(xx-info debian-arch)" | xargs -n 1 cp -vfp src/dash

FROM scratch as output
COPY --link --from=builder /release/ .

FROM scratch
ENTRYPOINT ["dash"]
COPY --link --from=builder /opt/dash/src/dash /usr/local/bin/dash
