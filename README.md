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
- **Strict Passive Mode**: Refactored logic to completely bypass Win32 Display API probes if active resolution switching is disabled. This prevents NVIDIA drivers from "snapping" the refresh rate from 165Hz to 144Hz.
- **Intelligent Alt-Tab Sync**: Automatically detects window focus and switches desktop resolution to match the game in real-time, preventing the "black screen hang" during Alt-Tabs.
- **Throttled Monitoring**: Optimized WMI Registry Watcher to ensure zero CPU impact during long gaming sessions.

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

> [!IMPORTANT] > **No coding knowledge required!** You can tune the launcher by editing a few simple numbers at the very top of the `.bat` file.

### 📝 How to Edit

1.  **Right-click** on your chosen `.bat` file (e.g., `both.bat`).
2.  Select **Edit** (this opens Notepad).
3.  Change the numbers next to the settings listed below.
4.  **Save (Ctrl+S)** and close.

### 🎛️ Settings Reference

| Setting            | Default Value | What it does                                                               |
| :----------------- | :------------ | :------------------------------------------------------------------------- |
| `$force_width`     | `1728`        | The horizontal width of the game. Use `-1` for auto.                       |
| `$force_height`    | `1080`        | The vertical height of the game. Use `-1` for auto.                        |
| `$force_refresh`   | `165`         | Your monitor's refresh rate. Use `-1` for auto.                            |
| `$force_exclusive` | `1`           | `1` = Maximum Performance (Fullscreen). `0` = Friendlier for Alt-Tabs.     |
| `$auto_start`      | `1`           | `1` = Instant launch. `0` = Shows menu first.                              |
| `$unify_cfg`       | `1`           | `1` = Uses the same settings for all of your Steam accounts.               |
| `$do_not_match`\*  | `1`           | `1` = Script only optimizes Steam. `0` = Script also manages your Desktop. |

_\*Found as `$do_not_set_desktop_res_to_match_game` in the file._

---

## 🧠 Technical Deep Dive

### Proximity-Based Refresh Matching

Standard scripts often fail if the monitor reports non-integer timings (e.g., 164.9Hz). Our updated `SetRes` library uses a proximity-based matching algorithm that prioritizes the absolute closest available mode, ensuring you always lock onto your maximum hardware capability (e.g., 165Hz) instead of falling back to standard 144Hz modes.

```csharp
var set = Refresh == 0 ? filtered.FirstOrDefault() : filtered.OrderBy(d => Math.Abs((decimal)d.Refresh - Refresh)).FirstOrDefault();
```

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
