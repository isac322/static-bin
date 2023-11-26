[1]: https://ghcr.io/isac322/curl

# Statically built curl

[![](https://ghcr-badge.egpl.dev/isac322/curl/tags?trim=major)][1]
[![](https://ghcr-badge.egpl.dev/isac322/curl/latest_tag?trim=major&label=latest)][1]
[![](https://ghcr-badge.egpl.dev/isac322/curl/size)][1]

[curl](https://github.com/curl/curl) is a A command line tool and library for transferring data with URL syntax, supporting DICT, FILE, FTP, FTPS, GOPHER, GOPHERS, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, MQTT, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET, TFTP, WS and WSS.

## Supported features

> ⚠ The binary size of full version is larger than slim version.  
> For smaller curl, visit https://github.com/isac322/static-bin/tree/HEAD/curl-slim

`docker run --rm -ti ghcr.io/isac322/curl:latest --version`

```
curl 8.2.1 (aarch64-alpine-linux-musl) libcurl/8.2.1 OpenSSL/3.1.4 zlib/1.2.13 brotli/1.1.0 zstd/1.5.5 c-ares/1.19.1 libssh2/1.10.0 nghttp2/1.57.0
Release-Date: 2023-07-26
Protocols: dict file ftp ftps gopher gophers http https imap imaps mqtt pop3 pop3s rtsp scp sftp smb smbs smtp smtps telnet tftp ws wss
Features: alt-svc AsynchDNS brotli HSTS HTTP2 HTTPS-proxy IPv6 Largefile libz NTLM NTLM_WB SSL threadsafe TLS-SRP UnixSockets zstd
```

## Download binary directly

```bash
wget -O curl-full "https://github.com/isac322/static-bin/releases/download/curl-full/$(uname -m)"
chmod +x curl-full
```

## Docker image

`docker pull ghcr.io/isac322/curl:latest`

### Contents

```
/
└── usr
    └── local
        └── bin
            └── curl
```

### Supported platforms

- `linux/amd64`
- `linux/arm64/v8`
- `linux/arm/v7`
- `linux/arm/v6`
- `linux/ppc64le`
- `linux/s390x`
- `linux/riscv64`

---


configure: Configured to build curl/libcurl:

```
Host setup:       aarch64-alpine-linux-musl
Install prefix:   /usr/local
Compiler:         xx-clang
 CFLAGS:          -Oz -ffunction-sections -fdata-sections -fno-unwind-tables -fno-asynchronous-unwind-tables -flto=thin -Qunused-arguments -O2 -Wno-pointer-bool-conversion
 CPPFLAGS:        -isystem /aarch64-alpine-linux-musl/usr/include -isystem /aarch64-alpine-linux-musl/usr/include -isystem /aarch64-alpine-linux-musl/usr/include -isystem /aarch64-alpine-linux-musl/usr/include -isystem /aarch64-alpine-linux-musl/usr/include -isystem /aarch64-alpine-linux-musl/usr/include -isystem /aarch64-alpine-linux-musl/usr/include -isystem /aarch64-alpine-linux-musl/usr/include
 LDFLAGS:         -Wl,-O2 -Wl,-s -Wl,-Bsymbolic -Wl,--gc-sections -lbrotlicommon --static -v -L/aarch64-alpine-linux-musl/lib -L/aarch64-alpine-linux-musl/usr/lib -L/aarch64-alpine-linux-musl/usr/lib -L/aarch64-alpine-linux-musl/usr/lib -L/aarch64-alpine-linux-musl/usr/lib -L/aarch64-alpine-linux-musl/usr/lib -L/aarch64-alpine-linux-musl/usr/lib -L/aarch64-alpine-linux-musl/usr/lib
 LIBS:            -lcares -lnghttp3 -lnghttp2 -lssh2 -L/aarch64-alpine-linux-musl/usr/lib -lssh2 -lssl -lcrypto -lssl -lcrypto -lzstd -lzstd -lbrotlidec -lbrotlidec -lz

curl version:     8.2.1
SSL:              enabled (OpenSSL v3+)
SSH:              enabled (libSSH2)
zlib:             enabled
brotli:           enabled (libbrotlidec)
zstd:             enabled (libzstd)
GSS-API:          no      (--with-gssapi)
GSASL:            no      (libgsasl not found)
TLS-SRP:          enabled
resolver:         c-ares
IPv6:             enabled
Unix sockets:     enabled
IDN:              no      (--with-{libidn2,winidn})
Build libcurl:    Shared=no, Static=yes
Built-in manual:  enabled
--libcurl option: enabled (--disable-libcurl-option)
Verbose errors:   enabled (--disable-verbose)
Code coverage:    disabled
SSPI:             no      (--enable-sspi)
ca cert bundle:   /etc/ssl/certs/ca-certificates.crt
ca cert path:     no
ca fallback:      no
LDAP:             no      (--enable-ldap / --with-ldap-lib / --with-lber-lib)
LDAPS:            no      (--enable-ldaps)
RTSP:             enabled
RTMP:             no      (--with-librtmp)
PSL:              no      (libpsl not found)
Alt-svc:          enabled (--disable-alt-svc)
Headers API:      enabled (--disable-headers-api)
HSTS:             enabled (--disable-hsts)
HTTP1:            enabled (internal)
HTTP2:            enabled (nghttp2)
HTTP3:            enabled (ngtcp2 + nghttp3)
ECH:              no      (--enable-ech)
WebSockets:       enabled
Protocols:        DICT FILE FTP FTPS GOPHER GOPHERS HTTP HTTPS IMAP IMAPS MQTT POP3 POP3S RTSP SCP SFTP SMB SMBS SMTP SMTPS TELNET TFTP WS WSS
Features:         AsynchDNS HSTS HTTP2 HTTPS-proxy IPv6 Largefile NTLM NTLM_WB SSL TLS-SRP UnixSockets alt-svc brotli libz threadsafe zstd

WARNING:  HTTP3 Websockets enabled but marked EXPERIMENTAL. Use with caution!
```