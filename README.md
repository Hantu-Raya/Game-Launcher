# Steam Game Launchers 🚀

![Status](https://img.shields.io/badge/Status-Ultra--Optimized-brightgreen)
![OS](https://img.shields.io/badge/OS-Windows-blue)
![Shell](https://img.shields.io/badge/Shell-PowerShell_&_Batch-darkblue)
![Logic](https://img.shields.io/badge/Logic-C%23-purple)

## 📖 Introduction

A collection of ultra-low-latency, hybrid Batch/PowerShell launchers designed for **Counter-Strike 2** and **Deadlock**. These scripts are engineered to strip away Steam's background bloat, enforce perfect display settings via direct Win32 API calls, and ensure a "bare-metal" gaming experience.

Derived from the original scripts by [AveYo](https://github.com/AveYo/Gaming/blob/main/CS2_launcher.bat), this version is heavily optimized with advanced state preservation, high-precision refresh rate rounding, and intelligent Alt-Tab resolution restoration.

> **The Goal:** Minimize input lag, eliminate HUD/Refresh rate mismatches, and provide a seamless one-click environment setup for competitive play.

---

## ✨ Features

### 🛠️ Core Optimizations

- **"Nuclear" Steam Stripping**: Restarts Steam with aggressive flags (`-cef-single-process`, `-noshaders`, etc.) to reduce RAM/CPU footprint by up to 70%.
- **Direct Win32 Display Control**: Uses an embedded C# library (`AveYo.SetRes`) to bypass the slow Windows Settings UI and communicate directly with display drivers.
- **Robust State Restoration**: Captures immutable native desktop constants _before_ launch. This prevents "refresh rate snapping" and ensures a perfect return to your desktop environment.
- **Intelligent Alt-Tab Sync**: Automatically detects window focus and switches desktop resolution to match the game in real-time, preventing the "black screen hang" during Alt-Tabs.
- **Throttled Monitoring**: Optimization of the WMI Registry Watcher to ensure zero CPU impact during long gaming sessions.

### 🎮 Game-Specific Logic

- **CS2 (`cs2.bat`)**: Automated `video.txt` injection, custom launch options, and auto-exec management.
- **Deadlock (`dead.bat`)**: Citadel-specific environment overrides, custom convars, and FSO (Fullscreen Optimization) management.
- **Universal Launcher (`both.bat`)**: A centralized hub with an interactive menu to launch either game with global settings.

---

## 🛠️ Tech Stack

| Component       | Technology   | Role                                                    |
| :-------------- | :----------- | :------------------------------------------------------ |
| **Wrapper**     | Batch (.bat) | Initial execution and CMD compatibility.                |
| **Logic**       | PowerShell   | Steam VDF parsing, Registry tasks, and orchestration.   |
| **Performance** | C# (.NET)    | Direct Win32 API access for high-speed display changes. |
| **Data**        | VDF / ACF    | Deep integration with Steam's configuration files.      |

---

## 🚀 How to Run

### 1. Installation

Simply download the script(s) and place them anywhere. On first run, the script will:

1.  Locate your Steam installation via the Registry.
2.  Perform a **Critical Path Validation** to ensure the game is installed and accessible.
3.  Extract and compile the embedded **SetRes** library (`SetRes.dll`) using `csc.exe`.
4.  Self-install a copy to your game folder for faster subsequent launches.

### 2. Basic Usage

- **Manual Launch**: Double-click any `.bat` file.
- **Universal Launch**: Run `both.bat` to see the game selection menu.
- **Auto-Start**: Run with `$auto_start = 1` to skip menus and launch immediately.

### 3. FSO Management

The scripts automatically handle **Fullscreen Optimizations**.

- If `enable_fso = 1`: Allows Windows to manage windowed/fullscreen mixing (better for DX11/Vulkan).
- If `enable_fso = 0`: "Nuclear" registry override to force true exclusive fullscreen behavior.

---

## ⚙️ Customization

You can easily modify the behavior of the launchers by editing the variables at the top of each `.bat` file:

### 🖥️ Display Overrides

- `$force_width` / `$force_height`: Set your desired game resolution (e.g., `1728` x `1080`). Set to `-1` for auto-detection.
- `$force_refresh`: Force a specific refresh rate (e.g., `240`).
- `$force_exclusive`: Set to `1` for True Exclusive Fullscreen, or `0` for Borderless/Desktop Friendly mode.

### 🕹️ Launcher Behavior

- `$auto_start`: Set to `1` to bypass all initial setup checks and launch the game instantly.
- `$unify_cfg`: Set to `1` to share the same video/keybind configs across all Steam accounts on your PC.
- `$do_not_set_desktop_res_to_match_game`: Set to `1` if you only want the Steam optimizations and don't want the script to touch your desktop resolution.

### 🚀 Advanced Tweaks

- `$STEAM_OPTIMIZED_ARGS`: Modify this string in `both.bat` to add or remove Steam client stripping flags.
- `$extra_launch_options`: Located within the game selection logic, this allows you to add custom game-specific flags like `-vulkan` or `-high`.

---

## 🧠 Technical Deep Dive

### High-Precision Refresh Rates

Standard scripts often truncate refresh rates (e.g., 164.9Hz becoming 164Hz). Our launchers use high-precision mathematical rounding to ensure Steam's internal fixed-point Hz format perfectly matches your hardware:

```powershell
$denom = 1000; $numer = [int]([math]::Round($refresh * $denom))
```

### The "Snap" Prevention

The launcher captures your native state as constant variables _before_ any mutation occurs. This protects against driver "snapping" where Windows might revert to a lower default refresh rate (like 144Hz) if the GPU driver resets during game initialization. Your desktop will always return to its true native state (e.g., 165Hz, 240Hz, or 360Hz).

### Performance Engineering

- **Lazy Compilation**: The C# source is only compiled if the DLL is missing or outdated, saving hundreds of milliseconds on every launch.
- **Scoped Variables**: Critical internal strings are cached at script-scope to minimize heap allocations and function call overhead.
- **Silent Hygiene**: All background operations (`robocopy`, `mkdir`, etc.) are fully silenced (stdout/stderr) for a clean, professional console output.

---

## 📋 Requirements

- **Windows 10/11 x64**
- **Steam** installed and logged in.
- **.NET Framework** for real-time C# compilation.

---

_Disclaimer: This project is a utility collection. Use at your own risk. Not affiliated with Valve or Steam._
