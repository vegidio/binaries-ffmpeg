# binaries-ffmpeg

A repository with static and dynamic binaries for **FFmpeg** — the `libavcodec`,
`libavformat`, `libavutil`, `libavfilter`, `libavdevice`, `libswscale`,
`libswresample` and `libpostproc` libraries, plus all of their dependencies
(headers included).

It offers x64 & arm64 versions for macOS, Linux and Windows. Static libraries use
the `.a` extension on every OS (all builds use GCC/Clang toolchains — MinGW on
Windows); dynamic libraries are `.so` (Linux), `.dylib` (macOS) and `.dll`
(Windows).

The binaries are **not** meant to be run as the `ffmpeg`/`ffplay`/`ffprobe`
applications — only the libraries and headers are produced, for linking into
other projects.

## Build

Binaries are built from [vcpkg](https://vcpkg.io) (pinned to release
`2026.06.24`, which ships **FFmpeg 8.1.2**) with the feature set:

```
ffmpeg[all-gpl,openssl,drawtext,vaapi,zmq,dvdvideo]
```

This enables every redistributable feature the vcpkg port can build consistently
across all platforms. `vaapi` is a Linux-only hardware-accel API and is omitted on
macOS/Windows. The non-redistributable `fdk-aac` (HE-AAC) feature is intentionally
excluded.

## License

The resulting binaries are licensed under **GPLv3** (FFmpeg under GPLv3 via
`--enable-version3`, combined with OpenSSL 3.x / Apache-2.0).

## 👨🏾‍💻 Author

Vinicius Egidio ([vinicius.io](http://vinicius.io))
