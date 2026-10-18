# ffmpeg-minimal-build

[![GitHub stars](https://img.shields.io/github/stars/Abdullah-Masood-05/ffmpeg-minimal-build?style=social&logo=github)](https://github.com/Abdullah-Masood-05/ffmpeg-minimal-build/stargazers)

> ⭐ **Enjoying ffmpeg-minimal-build?** If it's useful to you, please [star it on GitHub](https://github.com/Abdullah-Masood-05/ffmpeg-minimal-build). It's free and it helps other developers find it.

A minimal static build of FFmpeg 7.1.1 for Windows x64, compiled from the
`n7.1.1` source tree. Single 1.6 MB `ffmpeg.exe`, statically linked, no DLLs.

It does one job: open a Windows webcam through DirectShow and hand the caller
raw frames. That is the entire feature set. The build strips out everything
else, which is why the binary is so small.

## What it does

- DirectShow capture input (`--enable-indev=dshow`)
- MJPEG and rawvideo decoding, rawvideo encoding and muxing
- The `scale` filter for resizing
- Pipe protocol for streaming frames out of ffmpeg

## What it does not do

No network protocols, no ffplay, no ffprobe, no zlib, no iconv, no debug
symbols, no docs. The `--disable-all` base plus re-enabling only the pieces
above keeps the binary portable and the attack surface tiny.

## Why it exists

DeepScreen uses this in its desktop app to read camera frames for the native
Rust proctoring pipeline. The main application repo is private, so this
repository exists to publish the binary and the exact build recipe that
produced it. The public project where it is used:

https://github.com/Abdullah-Masood-05/deepscreen-viewer

## Build recipe

`build.sh` is the entire build. It clones FFmpeg at the `n7.1.1` tag, runs the
configure command with exactly the flags this binary was built with, builds,
and drops `dist/ffmpeg.exe` for you. Run it from an MSYS2/MinGW64 shell:

```
./build.sh
```

The custom part is the configure flag set inside the script: start from
`--disable-all` and re-enable only the DirectShow capture pieces. That is the
whole difference between this binary and a stock FFmpeg build, and it is the
only code this repository holds.

## Downloads

### With pixi (recommended)

The binaries are published as the `ffmpeg-minimal` conda package on the
[`abdullahmasood05/vigilo`](https://prefix.dev/channels/@abdullahmasood05/vigilo)
prefix.dev channel, for `win-64`, `linux-64` and `osx-arm64`:

```bash
pixi workspace channel add --prepend https://prefix.dev/abdullahmasood05/vigilo
pixi add ffmpeg-minimal
pixi run ffmpeg -version
```

or, as a global tool:

```bash
pixi global install -c https://prefix.dev/abdullahmasood05/vigilo -c conda-forge ffmpeg-minimal
```

The package is a straight repackaging of the release binaries below, pinned by
sha256 in [`conda/recipe.yaml`](conda/recipe.yaml) and uploaded by the
[`Publish conda package`](.github/workflows/publish-conda.yml) workflow.
It installs an executable named `ffmpeg`, so don't put it in the same
environment as conda-forge's full `ffmpeg`.

### Direct download

Grab the binary for your platform from the [releases](../../releases) page
(`ffmpeg-windows-x86_64.exe`, `ffmpeg-linux-x86_64`, `ffmpeg-macos-arm64`, plus
`.deb` / `.rpm` / `.pkg.tar.zst` packages for Linux). The checksum is listed in
each release.

## License

The FFmpeg source and this build are licensed under LGPL-2.1-or-later. See
[LICENSE](LICENSE). All credit for the underlying work belongs to the FFmpeg
developers; this repository only repackages a build of their software for a
specific use case.