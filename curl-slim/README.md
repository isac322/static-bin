[1]: https://ghcr.io/isac322/curl

# Statically built curl (Slim)

[![](https://ghcr-badge.egpl.dev/isac322/curl/tags?trim=major)][1]
[![](https://ghcr-badge.egpl.dev/isac322/curl/latest_tag?trim=major&label=latest)][1]
[![](https://ghcr-badge.egpl.dev/isac322/curl/size)][1]

[curl](https://github.com/curl/curl) is a A command line tool and library for transferring data with URL syntax, supporting DICT, FILE, FTP, FTPS, GOPHER, GOPHERS, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, MQTT, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET, TFTP, WS and WSS.

## Supported features

> ⚠ Slim version only supports http protocol.  
> For full featured curl, visit https://github.com/isac322/static-bin/tree/HEAD/curl-full

Output of `docker run --rm -ti ghcr.io/isac322/curl:slim --version`:

```
curl 8.3.0 (x86_64-alpine-linux-musl) libcurl/8.3.0
Release-Date: 2023-09-13
Protocols: http
Features: alt-svc IPv6 Largefile threadsafe UnixSockets
```

## Download binary directly

```bash
wget -O curl-slim "https://github.com/isac322/static-bin/releases/download/curl-slim/$(uname -m)"
chmod +x curl-slim
```

## Docker image

`docker pull ghcr.io/isac322/curl:slim`

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
Host setup:       x86_64-alpine-linux-musl
Install prefix:   /usr/local
Compiler:         xx-clang
 CFLAGS:          -ffunction-sections -flto=thin -fdata-sections -Qunused-arguments -O2 -Wno-pointer-bool-conversion
 CPPFLAGS:        
 LDFLAGS:         -Wl,--gc-sections -s -flto=thin --static
 LIBS:            

curl version:     8.3.0
SSL:              no      (--with-{openssl,gnutls,mbedtls,wolfssl,schannel,secure-transport,amissl,bearssl,rustls} )
SSH:              no      (--with-{libssh,libssh2})
zlib:             no      (--with-zlib)
brotli:           no      (--with-brotli)
zstd:             no      (--with-zstd)
GSS-API:          no      (--with-gssapi)
GSASL:            no      (libgsasl not found)
TLS-SRP:          no      (--enable-tls-srp)
resolver:         default (--enable-ares / --enable-threaded-resolver)
IPv6:             enabled
Unix sockets:     enabled
IDN:              no      (--with-{libidn2,winidn})
Build libcurl:    Shared=no, Static=yes
Built-in manual:  no      (--enable-manual)
--libcurl option: no
Verbose errors:   enabled (--disable-verbose)
Code coverage:    disabled
SSPI:             no      (--enable-sspi)
ca cert bundle:   no
ca cert path:     
ca fallback:      
LDAP:             no      (--enable-ldap / --with-ldap-lib / --with-lber-lib)
LDAPS:            no      (--enable-ldaps)
RTSP:             no      (--enable-rtsp)
RTMP:             no      (--with-librtmp)
PSL:              no      (libpsl not found)
Alt-svc:          enabled (--disable-alt-svc)
Headers API:      enabled (--disable-headers-api)
HSTS:             no      (--enable-hsts)
HTTP1:            enabled (internal)
HTTP2:            no      (--with-nghttp2, --with-hyper)
HTTP3:            no      (--with-ngtcp2 --with-nghttp3, --with-quiche, --with-msh3)
ECH:              no      (--enable-ech)
WebSockets:       no      (--enable-websockets)
Protocols:        HTTP
Features:         IPv6 Largefile UnixSockets alt-svc threadsafe
```