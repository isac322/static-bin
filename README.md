[1]: https://github.com/isac322/static-bin/tree/HEAD/dash
[2]: https://github.com/isac322/static-bin/tree/HEAD/gnu-tar
[3]: https://github.com/isac322/static-bin/tree/HEAD/zstd-slim
[4]: https://github.com/isac322/static-bin/tree/HEAD/zstd-full
[5]: https://github.com/isac322/static-bin/tree/HEAD/curl-slim
[6]: https://github.com/isac322/static-bin/tree/HEAD/curl-full

# Statically built executables

💻 Multi-arch, 📦 Small, 🔗 single binary, 🌐 Universal ➡ ⚡ Extreme portability

## Welcome

Install and run common programs without any package manager or compilation.

- Docker image provided ([images](https://github.com/isac322?tab=packages&repo_name=static-bin))
- Single file download available ([releases](https://github.com/isac322/static-bin/releases))
- Support multiple CPU architectures
- Very small executable (less than 1MB)
- Safe to use in scratch or distroless image

You can be freed from worrying about program installation inside CI or Docker image size and limitations comes from libc!

## Supported binaries

- [dash][1]
- [gnu-tar][2]
- [zstd][4] ([slim][3])
- [curl][6] ([slim][5])

## Usage

For example [gnu-tar][2]:

#### Option 1: Download binary directly

```bash
wget -O gnu-tar "https://github.com/isac322/static-bin/releases/download/gnu-tar/$(uname -m)"
chmod +x gnu-tar
gnu-tar --help
```

<details open>
<summary>if you use alpine linux along side with apk</summary>

You can also use `cat /etc/apk/arch` instead of `uname -m`.

```bash
wget -O gnu-tar "https://github.com/isac322/static-bin/releases/download/gnu-tar/$(cat /etc/apk/arch)"
```
</details>

#### Option 2: Install binary onto docker image

```Dockerfile
FROM ghcr.io/isac322/gnu-tar:latest as gnu-tar
# `scratch` can also be used instead of `distroless/static`
FROM gcr.io/distroless/static
# you can use `tar` inside this stage (`tar` will be located on `/usr/local/bin/tar`)
COPY --from=gnu-tar / /
# not required; usage example
ENTRYPOINT ["tar"]
```

---

You can check the detailed usages for each executable file in [Supported binaries](#supported-binaries).



## What is statically built executables?

[Wikipedia: Static build](https://en.wikipedia.org/wiki/Static_build)  
[Reddit: Static and Dynamic binaries?](https://www.reddit.com/r/linux/comments/6pkzf5/comment/dkq58n6/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)

**TL;DR; Single file & Distribution agnostic (e.g. you can use same file in Debian based or Alpine based linux)**

When you compile a program without specific options, you will get a dynamic binary that does not contains external binaries (so called .so files).
So OS must load those external binaries when you launch dynamic binary from filesystem.
On the other hand, static binary, copy all external binaries into single file.
So you do not have to install external libraries, just download and execute it.  
⚠ Static binaries depends on OS and CPU architecture.


## Why is it smaller than dynamic binary?

Usually static binaries are bigger than dynamic binaries because of its nature.
But we can (1) remove unused parts from binary using [strip](https://man7.org/linux/man-pages/man1/strip.1.html),
(2) compress binary using [UPX](https://upx.github.io/). 
