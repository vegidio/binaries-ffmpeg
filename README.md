# binaries-ffmpeg

A repository with static and dynamic binaries for **FFmpeg** — the `libavcodec`,
`libavformat`, `libavutil`, `libavfilter`, `libavdevice`, `libswscale`,
`libswresample` and `libpostproc` libraries, plus all of their dependencies
(headers included).

The binaries are **not** meant to be run as the `ffmpeg`/`ffplay`/`ffprobe`
applications — only the libraries and headers are produced, for linking into
other projects.

## Platforms

x64 & arm64 for macOS, Linux and Windows (6 targets). Static libraries use the
`.a` extension on every OS (all builds use GCC/Clang toolchains — MinGW on
Windows); dynamic libraries are `.so` (Linux), `.dylib` (macOS) and `.dll`
(Windows).

Built from [vcpkg](https://vcpkg.io), pinned to release `2026.06.24` (**FFmpeg
8.1.2**). Only **Release** binaries are produced (Debug is skipped).

## License

The binaries are licensed under **GPLv3** (FFmpeg under GPLv3 via
`--enable-version3`, combined with OpenSSL 3.x / Apache-2.0).

## Feature matrix

Legend: ✅ available · ❌ not available. Columns are `OS-arch`.

### Codecs

| Feature | What it is | mac-x64 | mac-arm64 | lin-x64 | lin-arm64 | win-x64 | win-arm64 |
|---|---|:--:|:--:|:--:|:--:|:--:|:--:|
| aom | AV1 encode/decode | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| dav1d | AV1 decode (fast) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| openh264 | H.264 encode/decode | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| theora | Theora video | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| x264 | H.264 encode | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| x265 | HEVC/H.265 encode | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| vpx | VP8/VP9 encode/decode | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| mp3lame | MP3 encode | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| opus | Opus audio | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| vorbis | Vorbis audio | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| speex | Speex audio | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| twolame | MP2 encode | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| ilbc | iLBC speech | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| openjpeg | JPEG 2000 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| webp | WebP images | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |

### Subtitles, text & filters

| Feature | What it is | mac-x64 | mac-arm64 | lin-x64 | lin-arm64 | win-x64 | win-arm64 |
|---|---|:--:|:--:|:--:|:--:|:--:|:--:|
| ass | libass subtitle rendering | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| freetype | font rendering | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| fontconfig | font discovery | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| fribidi | bidirectional text | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| drawtext | drawtext filter (harfbuzz) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| soxr | high-quality resampling | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| rubberband | audio time-stretch/pitch | ✅ | ✅ | ✅ | ✅ | ⚠️¹ | ⚠️¹ |

### Containers, protocols & misc

| Feature | What it is | mac-x64 | mac-arm64 | lin-x64 | lin-arm64 | win-x64 | win-arm64 |
|---|---|:--:|:--:|:--:|:--:|:--:|:--:|
| openssl | TLS (https/tls/rtmps) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| srt | Haivision SRT protocol | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| ssh | SFTP protocol (libssh) | ✅ | ❌ | ✅ | ❌ | ✅ | ❌ |
| zmq | ZeroMQ filters | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| dvdvideo | DVD-Video demuxer | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| xml2 | DASH demuxing (libxml2) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| sdl2 | SDL2 (avdevice output) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| snappy | Snappy (HAP) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| bzip2 / lzma / zlib | compression | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| iconv | charset conversion | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| avisynthplus | AviSynth+ script demuxer | ❌ | ❌ | ❌ | ❌ | ⚠️² | ❌ |

### Hardware acceleration / GPU

| Feature | What it is | mac-x64 | mac-arm64 | lin-x64 | lin-arm64 | win-x64 | win-arm64 |
|---|---|:--:|:--:|:--:|:--:|:--:|:--:|
| vulkan | Vulkan compute/codecs | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| opengl | OpenGL output | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| amf | AMD AMF encode | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| nvcodec | NVIDIA NVENC/NVDEC | ❌ | ❌ | ✅ | ✅ | ✅ | ❌ |
| qsv | Intel Quick Sync (oneVPL) | ❌ | ❌ | ✅ | ❌ | ✅ | ❌ |
| vaapi | VA-API (Linux) | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ |
| opencl | OpenCL filters | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| alsa | ALSA audio I/O | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ |

¹ **rubberband on Windows: dynamic builds only.** Its vcpkg port is unsupported on
`windows & static`, so it is present in the `.dll` builds but absent from the `.a`
builds.
² **avisynthplus: Windows x64 dynamic build only** (the port requires
`windows & !static`, and is unavailable on Windows-ARM).

## Why some features are missing

- **x264 / x265 / vpx on Windows-ARM** — these encoder ports do not build for
  `arm64-mingw` in vcpkg (they rely on x86 assembly / lack ARM-Windows support).
  AV1 (aom/dav1d) and the other decoders are still available there.
- **ssh on arm64** — the `libssh` vcpkg port is marked unsupported on arm.
- **opencl on macOS** — Apple deprecated OpenCL (since 10.14, in favour of Metal),
  and vcpkg only provides the Khronos ICD loader rather than Apple's
  `OpenCL.framework`, so it would find no device at runtime. (On Windows, OpenCL
  *is* enabled — vcpkg ships the loader as `OpenCL.a`/`OpenCL.dll.a`, so the build
  adds a `libOpenCL.*` alias for FFmpeg's `-lOpenCL` link test.)
- **amf / nvcodec / qsv / vaapi / alsa on macOS** — these backends are
  Windows/Linux technologies and are not provided on Apple platforms.
- **nvcodec / opengl on Windows-ARM, qsv on arm** — unsupported by the respective
  vcpkg ports on those targets.

## Features intentionally excluded everywhere

| Feature | Reason |
|---|---|
| `fdk-aac` (HE-AAC) | Non-redistributable / patent-encumbered; the built-in AAC codec covers AAC-LC. |
| `tensorflow` (DNN filter) | vcpkg port fails to build (and is very large). |
| `tesseract` (OCR filter) | Heavy; dropped to avoid failures in the dynamic builds. |
| `modplug` | libmodplug (C++) fails to link into FFmpeg (C) on MinGW. |
| `openmpt` | Pulls `mpg123`, which has an upstream ARM build bug. |
| `avresample` | Removed from FFmpeg in 5.0. |
| `ffmpeg` / `ffplay` / `ffprobe` apps | Only libraries are produced. |

> Dropping `modplug` + `openmpt` means there is **no tracker-module (MOD/XM/IT/S3M)
> decoding** in these builds.

## 👨🏾‍💻 Author

Vinicius Egidio ([vinicius.io](http://vinicius.io))
