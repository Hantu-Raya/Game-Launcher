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

### ⚡ Quick Start (30 Seconds)

1.  **Download** the script (`cs2.bat`, `dead.bat`, or `both.bat`).
2.  **Right-click -> Edit** the file to check your settings (Resolution/Hz).
3.  **Double-click** to run.
    - _Note: On the first run, you might see a black window with scrolling text for 5-10 seconds. This is normal!_

### 3. FSO Management

The scripts automatically handle **Fullscreen Optimizations**.

- If `enable_fso = 1`: Allows Windows to manage windowed/fullscreen mixing (better for DX11/Vulkan).
- If `enable_fso = 0`: "Nuclear" registry override to force true exclusive fullscreen behavior.

---

## ⚙️ Customization (Make it Yours)

> [!TIP] > **Don't be afraid!** You are only changing simple numbers at the top of the file. You cannot break your PC.

### 📝 Step-by-Step Guide

1.  **Right-click** the file (e.g., `dead.bat`) and select **Edit**.
    - _If asked, select "Notepad" as the program._
2.  Look for the **User Settings** block at the very top.
3.  Change the numbers to match your preference.
4.  **Save** (Ctrl+S) and close.

### 🎛️ Settings Cheat Sheet

| Setting              | Recommended | Description                                                       |
| :------------------- | :---------- | :---------------------------------------------------------------- |
| **$force_width**     | `-1`        | Your game width. `-1` uses your desktop default.                  |
| **$force_height**    | `-1`        | Your game height. `-1` uses your desktop default.                 |
| **$force_refresh**   | `-1`        | Your monitor's Hz. `-1` uses your desktop default.                |
| **$force_exclusive** | `1`         | `1` gives FPS boost (Exclusive). `0` is faster for Alt-Tab.       |
| **$auto_start**      | `1`         | `1` launches game instantly. `0` shows a menu first.              |
| **$unify_cfg**       | `1`         | `1` copies your pro settings to _all_ logged-in Steam accounts.   |
| **$do_not_match**    | `1`         | `1` = Optimize Steam only (Safer). `0` = Optimize Resolution too. |

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
