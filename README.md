# Veo Watermark Remover

[![Download](https://img.shields.io/github/v/release/allenk/VeoWatermarkRemover?label=Download&color=brightgreen)](https://github.com/allenk/VeoWatermarkRemover/releases/latest)
[![Demo](https://img.shields.io/badge/Status-Demo%20Build-orange.svg)](#demo-build-notice)
[![Platform](https://img.shields.io/badge/Platform-Windows%20|%20Linux%20|%20macOS-blue.svg)](#download)
[![Based on GWT](https://img.shields.io/badge/Based%20on-GeminiWatermarkTool-green.svg)](https://github.com/allenk/GeminiWatermarkTool)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://github.com/allenk/GeminiWatermarkTool/blob/main/LICENSE)

> **NEW: Watch the demo video on YouTube — [Veo Watermark Remover in Action](https://www.youtube.com/watch?v=bPDhD67C_rI)**

> [!WARNING]
> **This is a DEMO/PREVIEW build** — CLI only, unsigned executable, early testing phase.
> See [Demo Build Notice](#demo-build-notice) for details and known limitations.
> **Windows/macOS may block the executable on first run — see [First Run — OS Security Prompts](#first-run--os-security-prompts) below.**

This is a **demo/preview build** of the upcoming Veo watermark removal feature for [GeminiWatermarkTool](https://github.com/allenk/GeminiWatermarkTool). Once stabilized, this functionality will be merged into the main GWT release with full GUI support.

Remove the "Veo" text watermark from Google Veo-generated videos using **mathematically precise reverse alpha blending** — the same proven technique behind [GeminiWatermarkTool](https://github.com/allenk/GeminiWatermarkTool).

No cloud services. No AI hallucination. No quality loss. Just math.

## What's New (v0.4.0)

- **macOS stability fix** — addresses the SIGBUS crash on Apple Silicon reported in upstream GeminiWatermarkTool [#30](https://github.com/allenk/GeminiWatermarkTool/issues/30) / [#31](https://github.com/allenk/GeminiWatermarkTool/issues/31). The Vulkan loader is probed before NCNN's GPU init; if it's absent (the default on macOS without MoltenVK), AI denoise transparently falls back to CPU instead of crashing
- **`--snap-min-size`** — finer snap-search control on the image-watermark side (default 16, range 8-64), mirroring the existing `--snap-max-size` flag
- **End-to-end CI smoke test** — every CI build now runs `--denoise ai` through a real inference pipeline (Windows / Linux / macOS) before publishing. The previous `--version`-only check never touched the AI denoise init path, which is how the macOS crash slipped past CI in v0.3.0

## Previous Release Highlights (v0.3.0)

- **Improved watermark masks** — remastered 720p and 1080p alpha maps using golden frame-differencing from multiple video sources (10+ transition pairs)
- **Better output quality** — upgraded video encoder (High profile, B-frames, multi-reference)
- **Enhanced AI cleanup** — tuned per-resolution denoise parameters with expanded inference context
- **Progress bar** — real-time ASCII progress indicator during video processing
- **Quieter output** — reduced log verbosity in normal mode; use `--verbose` for detailed diagnostics

## How It Works

![Drag & Drop Demo](artworks/veo-watermark-remover.gif)

**Just drag and drop.** Drop your Veo video onto the executable — it automatically removes the watermark and outputs `video_processed.mp4` with audio preserved.

### Before / After

![Side by Side Comparison](artworks/GWT_VEO_Watermark_Removal_Demo.gif)

## Quick Start

### Drag & Drop (Windows — Easiest)

1. Download `GeminiWatermarkTool-Video.exe` (see [Download](#download) below)
2. Drag your `.mp4` file onto the exe
3. Done — output appears as `yourfile_processed.mp4` in the same folder

### Command Line (All Platforms)

```bash
# Simplest — auto-detect, auto-output
./GeminiWatermarkTool-Video video.mp4

# Specify output path
./GeminiWatermarkTool-Video -i video.mp4 -o cleaned.mp4

# Explicit Veo mode (same result)
./GeminiWatermarkTool-Video --veo -i video.mp4 -o cleaned.mp4
```

## Features

- **Single executable** — standalone, zero dependencies, zero installation
- **Cross-platform** — Windows, Linux, and macOS (Universal Binary)
- **Direct mp4-to-mp4** — no intermediate files, no external tools needed
- **Audio preserved** — original audio track is kept without re-encoding
- **AI denoise** — GPU-accelerated FDnCNN cleanup for residual artifacts (Vulkan)
- **Fast** — 720p at ~50 fps, 1080p at ~18 fps (Release build, Ryzen 9 + RTX)
- **Landscape + Portrait** — supports both orientations at 720p and 1080p
- **Progress bar** — real-time processing progress in the terminal

## Technical Details

This tool uses the same **reverse alpha blending** algorithm as [GeminiWatermarkTool](https://github.com/allenk/GeminiWatermarkTool):

```text
original = (watermarked - alpha * logo_value) / (1 - alpha)
```

The Veo watermark alpha maps were extracted using frame differencing from Veo-generated videos with watermark on/off transitions. Multiple golden pairs (Lightning flash, Light bulb, Sunrise scenes) were averaged for maximum accuracy. AI denoise (FDnCNN, NCNN + Vulkan GPU) handles residual edge artifacts from video compression.

For the full technical background on reverse alpha blending, see:
**[Removing Gemini AI Watermarks: A Deep Dive into Reverse Alpha Blending](https://allenkuo.medium.com/removing-gemini-ai-watermarks-a-deep-dive-into-reverse-alpha-blending-bbbd83af2a3f)**

## System Requirements

| Component | Requirement                                                         |
| --------- | ------------------------------------------------------------------- |
| OS        | Windows 10/11 x64, Linux x64, macOS (Intel + Apple Silicon)         |
| GPU       | Vulkan-capable GPU for AI Denoise acceleration (or fallback to CPU) |
| RAM       | 4 GB minimum                                                        |

> **Note:** AI denoise prefers a Vulkan GPU but transparently falls back to CPU when no Vulkan loader is available.
>
> **macOS users:** macOS does not ship Vulkan natively — install the [LunarG Vulkan SDK](https://vulkan.lunarg.com/) (which bundles MoltenVK) for GPU acceleration. Otherwise the tool runs in CPU mode by default; this is fully supported but slower than Vulkan inference.

## Download

> **Single file download — no installer, no setup.**

| Platform | File | Notes |
|----------|------|-------|
| **Windows x64** | `GeminiWatermarkTool-Video.exe` | Drag & drop supported |
| **Linux x64** | `GeminiWatermarkTool-Video` | `chmod +x` before running |
| **macOS Universal** | `GeminiWatermarkTool-Video` | Intel + Apple Silicon |

Download the latest release from the [Releases page](https://github.com/allenk/VeoWatermarkRemover/releases/latest).

### Verification

SHA256 checksums are provided on each release page. Verify with:

```bash
# Windows (PowerShell)
Get-FileHash .\GeminiWatermarkTool-Video.exe -Algorithm SHA256

# Linux / macOS
sha256sum GeminiWatermarkTool-Video
```

> **If you're not comfortable running an unsigned executable, that's completely fine.**
> This is a demo build for early testing. The Veo removal feature will be integrated into the main [GeminiWatermarkTool](https://github.com/allenk/GeminiWatermarkTool) release when ready, with full source code, CI/CD builds, and cross-platform support.

## First Run — OS Security Prompts

Downloaded binaries are not code-signed, so your OS may show a security warning on first launch. This is normal for open-source software distributed outside app stores.

<details>
<summary><b>Windows</b> — SmartScreen "Windows protected your PC"</summary>

**Option A:** Click **More info** → **Run anyway**.

**Option B (PowerShell):**

```powershell
Unblock-File .\GeminiWatermarkTool-Video.exe
```

</details>

<details>
<summary><b>macOS</b> — "Apple cannot check it for malicious software"</summary>

**Option A (recommended):** Right-click the binary → **Open** → click **Open** in the dialog. You only need to do this once.

**Option B (terminal):**

```bash
xattr -dr com.apple.quarantine GeminiWatermarkTool-Video
chmod +x GeminiWatermarkTool-Video
```

</details>

<details>
<summary><b>Linux</b> — No security prompt</summary>

Linux does not quarantine downloaded binaries. Just ensure the file is executable:

```bash
chmod +x GeminiWatermarkTool-Video
./GeminiWatermarkTool-Video video.mp4
```

</details>

## Demo Build Notice

This is a **preview/demo build** with the following limitations:

- **CLI only** — GUI support is planned for the main GWT release
- **Video only** — this build processes video files (.mp4/.mkv/.mov); for Gemini image watermarks, use the main [GeminiWatermarkTool](https://github.com/allenk/GeminiWatermarkTool)

### What's Next

When the Veo feature is stable, it will be merged into [GeminiWatermarkTool](https://github.com/allenk/GeminiWatermarkTool) as a unified release supporting both Gemini image and Veo video watermark removal.

## About GeminiWatermarkTool

[GeminiWatermarkTool](https://github.com/allenk/GeminiWatermarkTool) is an open-source tool for removing Gemini AI visible watermarks from images. It supports GUI + CLI dual-mode operation, cross-platform deployment (Windows / Linux / macOS / Android), and optional AI denoise with NCNN + Vulkan GPU acceleration.

**Author:** Allen Kuo ([@allenk](https://github.com/allenk))

## License

This demo build is based on [GeminiWatermarkTool](https://github.com/allenk/GeminiWatermarkTool) (MIT License).

It includes third-party open-source components (NCNN, OpenCV, etc.) under their respective permissive licenses. Full license texts are available in the respective project repositories.
