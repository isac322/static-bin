[1]: https://ghcr.io/isac322/curl

# Statically built curl (Slim)

[![](https://ghcr-badge.egpl.dev/isac322/curl/tags?trim=major)][1]
[![](https://ghcr-badge.egpl.dev/isac322/curl/latest_tag?trim=major&label=latest)][1]
[![](https://ghcr-badge.egpl.dev/isac322/curl/size)][1]

[curl](https://github.com/curl/curl) is a A command line tool and library for transferring data with URL syntax, supporting DICT, FILE, FTP, FTPS, GOPHER, GOPHERS, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, MQTT, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET, TFTP, WS and WSS.

## Supported features

> ⚠ Slim version only supports https protocol.  
> For full featured curl, visit https://github.com/isac322/static-bin/tree/HEAD/curl-full

`docker run --rm -ti ghcr.io/isac322/curl:slim --version`

```
curl 8.2.1 (x86_64-alpine-linux-musl) libcurl/8.2.1
Release-Date: 2023-07-26
Protocols: http
Features: alt-svc AsynchDNS HSTS IPv6 Largefile threadsafe UnixSockets
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