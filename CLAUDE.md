# CLAUDE.md

Guidance for working in this repository.

## What this is

A CI-only repository that produces **prebuilt FFmpeg libraries** (static `.a` and
dynamic `.so`/`.dylib`/`.dll`) for consumption by other projects. There is no
application source here — only GitHub Actions that build FFmpeg via vcpkg and
attach the results to GitHub Releases. Modeled on
[`vegidio/binaries-avif`](https://github.com/vegidio/binaries-avif).

## How it works

`/.github/workflows/build.yml` triggers on any pushed tag, runs three platform
jobs in a matrix, then a `release` job that downloads all artifacts and publishes
them with `ncipollo/release-action`.

Each platform job delegates to a composite action:

- `.github/actions/macos/action.yml` — builds **4** triplets on one runner:
  `x64-osx`, `x64-osx-dynamic`, `arm64-osx`, `arm64-osx-dynamic`.
- `.github/actions/linux/action.yml` — one arch per runner (`ubuntu-latest` = x64,
  `ubuntu-24.04-arm` = arm64); builds `<arch>-linux` (static) and
  `<arch>-linux-dynamic`.
- `.github/actions/windows/action.yml` — MSYS2 + MinGW (`MINGW64` x64,
  `CLANGARM64` arm64); builds `<prefix>-static` and `<prefix>-dynamic`.

Unlike the avif repo (which built deps via vcpkg then compiled libavif from
source), **FFmpeg is itself a vcpkg port**, so each action just runs
`vcpkg install ffmpeg[...]` for the static and dynamic triplets, then copies the
full `include/` + `lib/` tree (FFmpeg libs **and** all dependency libs) into
`static_<arch>/` and `dynamic_<arch>/`, zips them, and uploads.

Artifact/zip naming (kept identical to avif): `static_<os>_<arch>.zip` /
`dynamic_<os>_<arch>.zip`.

## Key decisions (don't silently change these)

- **vcpkg is pinned to `2026.06.24`** (FFmpeg **8.1.2**) in every action's cache
  key, comment header, and `git clone --branch`. Bump all of them together to
  upgrade.
- **Feature set:** `ffmpeg[all-gpl,openssl,drawtext,vaapi,zmq,dvdvideo]`.
  - `vaapi` is **Linux-only** (`supports: linux`, a Linux-only HW-accel API) — it
    is omitted from the macOS and Windows feature strings, or vcpkg errors out.
  - `rubberband` was **removed from all OSes** to keep the produced library set
    uniform: its port is unsupported on `windows & static`, and a feature present
    on some platforms but not others is inconsistent for downstream consumers.
  - Lesson: an explicitly-named feature that doesn't `support` a triplet aborts the
    whole vcpkg plan, unlike features pulled transitively by the platform-guarded
    `all-gpl` meta-feature. Explicit extras must build on **every** target triplet.
  - `openssl` transitively enables `version3` (`--enable-version3`). With `gpl`
    enabled, OpenSSL ≥3.0 *requires* version3 (FFmpeg's `configure` dies
    otherwise). Result: license `GPL version 3 or later` → **redistributable**.
  - Intentionally **excluded:** `fdk-aac` (nonfree/HE-AAC → non-redistributable),
    `avresample` (removed from FFmpeg in 5.0), and the `ffmpeg`/`ffplay`/`ffprobe`
    apps (libraries only).
- **Static libs are `.a` on every OS** (MinGW on Windows, not MSVC `.lib`).

## Known risks / first-run gotchas

- `all-gpl` transitively pulls **`tensorflow`** (into `x64-*-dynamic`) and
  **`tesseract`** (into all dynamic triplets) — both heavy and the most likely to
  fail or exceed CI time. If they break, the fallback is to replace `[all-gpl]`
  with an explicit curated feature list excluding them.
- Several vcpkg ports (`alsa` on Linux, `gperf` on macOS) build from source via
  autotools, so both actions install `autoconf autoconf-archive automake libtool`
  (`apt` / `brew`). On **Windows**, vcpkg fetches its own autotools via msys — no
  package change needed. vcpkg prints the exact missing program/package on
  failure — expand the install list from the error.
- `arm64-mingw` (Windows ARM) is the least-tested FFmpeg target; vcpkg's platform
  guards auto-drop unsupported features (e.g. x264/x265 on Windows ARM).
- The `actions/cache@v4` vcpkg binary cache makes cold builds (which compile
  x264/x265/aom/etc.) tolerable on re-runs.
