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
`2026.06.24`, which ships **FFmpeg 8.1.2**) with a curated, GPL-compatible feature
list covering all the common codecs, containers, protocols, filters and
hardware-accel backends: x264, x265, AV1 (aom/dav1d), VP8/9, MP3, Opus, Vorbis,
Theora, libass subtitles, drawtext, OpenSSL/TLS, SRT, SSH, zmq, DVD-Video, plus
vaapi/qsv/nvcodec/amf/vulkan/opencl/opengl where the platform supports them.

Platform-specific features are only enabled where they build (e.g. `vaapi`/`alsa`
are Linux-only, `qsv`/`ssh` are x64-only, `x264`/`x265` are unavailable on
Windows-ARM).

Intentionally **excluded:** `fdk-aac` (non-redistributable HE-AAC), and four
features whose ports cannot build cleanly across all platforms — `tensorflow`
(DNN filter), `tesseract` (OCR), and `modplug`/`openmpt` (tracker-module
decoders).

## License

The resulting binaries are licensed under **GPLv3** (FFmpeg under GPLv3 via
`--enable-version3`, combined with OpenSSL 3.x / Apache-2.0).

## 👨🏾‍💻 Author

Vinicius Egidio ([vinicius.io](http://vinicius.io))
