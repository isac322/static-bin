[1]: https://ghcr.io/isac322/dash

# Statically build dash (Debian Almquist Shell)

[![](https://ghcr-badge.egpl.dev/isac322/dash/tags?trim=major)][1]
[![](https://ghcr-badge.egpl.dev/isac322/dash/latest_tag?trim=major&label=latest)][1]
[![](https://ghcr-badge.egpl.dev/isac322/dash/size)][1]

[DASH](http://gondor.apana.org.au/~herbert/dash/) is a POSIX-compliant implementation of /bin/sh that aims to be as small as possible. It does this without sacrificing speed where possible. In fact, it is significantly faster than bash (the GNU Bourne-Again SHell) for most tasks.


## Download binary directly

```bash
wget -O dash "https://github.com/isac322/static-bin/releases/download/dash/$(uname -m)"
chmod +x dash
```

## Docker image

`docker pull ghcr.io/isac322/dash:0.5.12`

Note that if you are copying the dash to another stage in a multi stage build, you may need to make a symbolic link of `binsh` to the dash (`ln -s /usr/local/bin/dash /bin/sh`)

### Contents

```
/
└── usr
    └── local
        └── bin
            └── dash
```

### Supported platforms

- `linux/amd64`
- `linux/arm64/v8`
- `linux/arm/v7`
- `linux/arm/v6`
- `linux/ppc64le`
- `linux/s390x`
- `linux/riscv64`