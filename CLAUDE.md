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

- `.github/actions/macos/action.yml` — one arch per **native** runner (`macos-13`
  = x64, `macos-latest` = arm64); builds `<arch>-osx` (static) and
  `<arch>-osx-dynamic`.
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
- **Feature set:** an **explicit curated list** per triplet (NOT the `all-gpl`
  meta-feature). Each action defines a `BASE` of always-supported features and
  appends platform-gated ones, replicating vcpkg's own `all`/`all-gpl` guards.
  - Why not `all-gpl`: it transitively pulls ports that can't build across all 6
    triplets (`tensorflow`, `tesseract`, `modplug`, `openmpt`→`mpg123`) and a
    meta-feature gives **no way to exclude individual ports**. So we list features
    explicitly instead. See the `feature-set-decisions` memory.
  - **Per-triplet gating** (must stay correct — an explicitly-named feature that a
    triplet doesn't `support` aborts the whole vcpkg plan):
    - `alsa`, `vaapi` → Linux only.
    - `amf`, `opencl` → not macOS.
    - `opengl`, `nvcodec` → not Windows-ARM (nvcodec also not macOS).
    - `ssh` → x64 only (port unsupported on arm).
    - `qsv` → x64 + (Linux|Windows) only.
    - `x264`, `x265` → not Windows-ARM.
    - `avisynthplus` → Windows-x64-dynamic only.
    - `opencl` → Linux + Windows; not macOS (Apple deprecated it, vcpkg has only
      the ICD loader → no runtime device). On Windows the loader is shipped as
      `OpenCL.a`/`OpenCL.dll.a`, so the action pre-installs `opencl` and adds a
      `libOpenCL.*` alias so FFmpeg's `-lOpenCL` link test resolves.
    - `vpx` → everywhere except Windows-ARM (libvpx fails to build there).
    - `rubberband` → everywhere except Windows **static** (port unsupported on
      `windows & static`); on Windows it's added to the dynamic build only.
  - **Per-platform policy** (user-approved): a feature is enabled wherever it
    builds, not gated to the lowest common denominator. So Windows-ARM has the
    smallest set, and opencl is present on Linux/macOS but absent on Windows.
  - **Excluded:** `tensorflow`, `tesseract`, `modplug`, `openmpt` (won't build —
    user-approved), `fdk-aac` (nonfree/HE-AAC → non-redistributable), `avresample`
    (removed from FFmpeg in 5.0), and the apps (libraries only).
  - `openssl` transitively enables `version3` (`--enable-version3`). With `gpl`
    enabled, OpenSSL ≥3.0 *requires* version3 (FFmpeg's `configure` dies
    otherwise). Result: license `GPL version 3 or later` → **redistributable**.
  - `libxcrypt` (transitive Linux dep) needs `libltdl-dev`; `libva`/`vaapi` need
    `libdrm-dev` — both installed by the Linux action.
- **Release-only builds:** each action appends `set(VCPKG_BUILD_TYPE release)` to
  every triplet file after bootstrap, so vcpkg skips Debug (roughly halves build
  time).
- **Static libs are `.a` on every OS** (MinGW on Windows, not MSVC `.lib`).

## Known risks / first-run gotchas

- Adding a new feature to the curated list means checking its `supports`/platform
  constraints first — an unsupported explicit feature aborts the whole vcpkg plan.
- Several vcpkg ports (`alsa` on Linux, `gperf` on macOS) build from source via
  autotools, so both actions install `autoconf autoconf-archive automake libtool`
  (`apt` / `brew`). On **Windows**, vcpkg fetches its own autotools via msys — no
  package change needed. vcpkg prints the exact missing program/package on
  failure — expand the install list from the error.
- `arm64-mingw` (Windows ARM) is the least-tested FFmpeg target; its curated list
  is the smallest (no ssh/opengl/nvcodec/qsv/x264/x265).
- The `actions/cache@v4` vcpkg binary cache makes cold builds (which compile
  x264/x265/aom/etc.) tolerable on re-runs.
