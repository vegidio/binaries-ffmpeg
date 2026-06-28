# binaries-ffmpeg

A repository with static and dynamic binaries for **FFmpeg** — the `libavcodec`, `libavformat`, `libavutil`, `libavfilter`, `libavdevice`, `libswscale` and `libswresample` libraries, plus all of their dependencies (headers included).

The binaries are **not** meant to be run as the `ffmpeg`/`ffplay`/`ffprobe` applications — only the libraries and headers are produced, for linking into other projects.

## Platforms

x64 & arm64 for macOS, Linux and Windows, built with Clang (macOS), GCC/Clang (Linux) and — on Windows — **both MSVC and MinGW** toolchains (so each Windows arch ships two variants). Static libraries use the `.a` extension on macOS, Linux and Windows-MinGW, and the `.lib` extension on Windows-MSVC; dynamic libraries are `.so` (Linux), `.dylib` (macOS), `.dll` + import `.lib` (Windows-MSVC) and `.dll` (Windows-MinGW). The release archives are named `static_<os>_<arch>.zip` / `dynamic_<os>_<arch>.zip`, where `<os>` is `osx`, `linux`, `windows` (MSVC) or `mingw`.

Built from [vcpkg](https://vcpkg.io), pinned to release `2026.06.24` (**FFmpeg 8.1.2**). Only **Release** binaries are produced (Debug is skipped).

## License

The binaries are licensed under **GPLv3** (FFmpeg under GPLv3 via `--enable-version3`, combined with OpenSSL 3.x / Apache-2.0).

## Feature matrix

Legend: ✅ available · ❌ not available. Columns are `OS-arch`.

### Codecs

| Feature  | What it is            | mac-x64 | mac-arm64 | lin-x64 | lin-arm64 | win-x64 | win-arm64 | mingw-x64 | mingw-arm64 |
|----------|-----------------------|:-------:|:---------:|:-------:|:---------:|:-------:|:---------:|:---------:|:-----------:|
| aom      | AV1 encode/decode     |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| dav1d    | AV1 decode (fast)     |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| openh264 | H.264 encode/decode   |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| theora   | Theora video          |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| x264     | H.264 encode          |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ❌      |
| x265     | HEVC/H.265 encode     |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ❌      |
| vpx      | VP8/VP9 encode/decode |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ❌      |
| mp3lame  | MP3 encode            |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| opus     | Opus audio            |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| vorbis   | Vorbis audio          |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| speex    | Speex audio           |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| twolame  | MP2 encode            |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| ilbc     | iLBC speech           |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| openjpeg | JPEG 2000             |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| webp     | WebP images           |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |

### Subtitles, text & filters

| Feature    | What it is                 | mac-x64 | mac-arm64 | lin-x64 | lin-arm64 | win-x64 | win-arm64 | mingw-x64 | mingw-arm64 |
|------------|----------------------------|:-------:|:---------:|:-------:|:---------:|:-------:|:---------:|:---------:|:-----------:|
| ass        | libass subtitle rendering  |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| freetype   | font rendering             |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| fontconfig | font discovery             |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| fribidi    | bidirectional text         |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| drawtext   | drawtext filter (harfbuzz) |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| soxr       | high-quality resampling    |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |

### Containers, protocols & misc

| Feature             | What it is               | mac-x64 | mac-arm64 | lin-x64 | lin-arm64 | win-x64 | win-arm64 | mingw-x64 | mingw-arm64 |
|---------------------|--------------------------|:-------:|:---------:|:-------:|:---------:|:-------:|:---------:|:---------:|:-----------:|
| openssl             | TLS (https/tls/rtmps)    |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| srt                 | Haivision SRT protocol   |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| ssh                 | SFTP protocol (libssh)   |    ✅    |     ❌     |    ✅    |     ❌     |    ✅    |     ❌     |     ✅     |      ❌      |
| zmq                 | ZeroMQ filters           |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ❌      |
| dvdvideo            | DVD-Video demuxer        |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| xml2                | DASH demuxing (libxml2)  |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| sdl2                | SDL2 (avdevice output)   |    ✅    |     ✅     |    ❌    |     ❌     |    ✅    |     ✅     |     ✅     |      ✅      |
| snappy              | Snappy (HAP)             |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| bzip2 / lzma / zlib | compression              |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| iconv               | charset conversion       |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| avisynthplus        | AviSynth+ script demuxer |    ❌    |     ❌     |    ❌    |     ❌     |   ⚠️¹   |     ❌     |    ⚠️¹    |      ❌      |

### Hardware acceleration / GPU

| Feature | What it is                | mac-x64 | mac-arm64 | lin-x64 | lin-arm64 | win-x64 | win-arm64 | mingw-x64 | mingw-arm64 |
|---------|---------------------------|:-------:|:---------:|:-------:|:---------:|:-------:|:---------:|:---------:|:-----------:|
| vulkan  | Vulkan compute/codecs     |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| opengl  | OpenGL output             |    ✅    |     ✅     |    ✅    |     ✅     |    ✅    |     ❌     |     ✅     |      ❌      |
| amf     | AMD AMF encode            |    ❌    |     ❌     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| nvcodec | NVIDIA NVENC/NVDEC        |    ❌    |     ❌     |    ✅    |     ✅     |    ✅    |     ❌     |     ✅     |      ❌      |
| qsv     | Intel Quick Sync (oneVPL) |    ❌    |     ❌     |    ✅    |     ❌     |    ✅    |     ❌     |     ✅     |      ❌      |
| vaapi   | VA-API (Linux)            |    ❌    |     ❌     |    ✅    |     ✅     |    ❌    |     ❌     |     ❌     |      ❌      |
| opencl  | OpenCL filters            |    ❌    |     ❌     |    ✅    |     ✅     |    ✅    |     ✅     |     ✅     |      ✅      |
| alsa    | ALSA audio I/O            |    ❌    |     ❌     |    ✅    |     ✅     |    ❌    |     ❌     |     ❌     |      ❌      |

¹ **avisynthplus: Windows x64 dynamic build only** (the port requires `windows & !static`, and is unavailable on Windows-ARM).

## Why some features are missing

- **ssh on arm64** — the `libssh` vcpkg port is marked unsupported on arm (on Windows-ARM it is also dropped to keep the vcpkg install plan valid).
- **opencl on macOS** — Apple deprecated OpenCL (since 10.14, in favour of Metal), and vcpkg only provides the Khronos ICD loader rather than Apple's `OpenCL.framework`, so it would find no device at runtime. (On Windows OpenCL *is* enabled — MSVC links vcpkg's `OpenCL` loader normally; MinGW ships it as `OpenCL.a`/`OpenCL.dll.a`, so the build adds a `libOpenCL.*` alias for FFmpeg's `-lOpenCL` link test.)
- **amf / nvcodec / qsv / vaapi / alsa on macOS** — these backends are Windows/Linux technologies and are not provided on Apple platforms.
- **nvcodec / qsv on Windows-ARM, qsv on Linux-arm** — a hard `supports` exclusion: `ffnvcodec` has no `arm64-windows` and `libvpl` (oneVPL) is x86/x64-only.
- **opengl on Windows-ARM** — dropped (together with ssh) to keep the vcpkg install plan valid on `arm64-windows`.
- **x264 / x265 / vpx / zmq on Windows-ARM MinGW only** — these are ✅ on `win-arm64-msvc` but ❌ on `win-arm64-mingw`: the x264/x265/vpx encoder ports do not build for `arm64-mingw` in vcpkg (x86 assembly / no ARM-Windows support), and the `zeromq` v4.3.5 port fails to compile on arm64 (`use of undeclared identifier 'nsecs_per_usec'`). The MSVC toolchain builds all four for Windows-ARM.
- **sdl2 on Linux** — the vcpkg `sdl2` build links `-liconv`, but on glibc systems vcpkg's `libiconv` is a stub (no link library), so the link fails. SDL2 only provides the `sdl2` output device in `libavdevice`, which library consumers rarely need.

## Features intentionally excluded everywhere

| Feature                              | Reason                                                                                                                   |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| `fdk-aac` (HE-AAC)                   | Non-redistributable / patent-encumbered; the built-in AAC codec covers AAC-LC.                                           |
| `tensorflow` (DNN filter)            | vcpkg port fails to build (and is very large).                                                                           |
| `tesseract` (OCR filter)             | Heavy; dropped to avoid failures in the dynamic builds.                                                                  |
| `modplug`                            | Pulled by the `all-gpl` meta-feature but does not build cleanly across all target triplets.                              |
| `openmpt`                            | Pulls `mpg123`, which has an upstream ARM build bug.                                                                     |
| `rubberband`                         | Its v4.0.0 port fails to compile with modern compilers (`unknown type name 'size_t'`).                                   |
| `avresample`                         | Removed from FFmpeg in 5.0.                                                                                              |
| `libpostproc`                        | The vcpkg `ffmpeg` port exposes no `postproc` feature and never passes `--enable-postproc`, so the library is not built. |
| `ffmpeg` / `ffplay` / `ffprobe` apps | Only libraries are produced.                                                                                             |

> Dropping `modplug` + `openmpt` means there is **no tracker-module (MOD/XM/IT/S3M) decoding** in these builds.

## 👨🏾‍💻 Author

Vinicius Egidio ([vinicius.io](http://vinicius.io))
