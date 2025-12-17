# Word-Scrambler
 eine einfache Webseite zum Scramblen von Wörtern
# Realtime Glyph Scramble

A highly customizable **real-time typographic scramble engine** inspired by experimental loaders and kinetic typography (e.g. aidn.jp).  
Each character is scrambled individually, in real time, with fine-grained control over timing, typography, and visual behavior.

---

## ✨ Features

### 🔤 Realtime Character Scrambling
- Every typed character scrambles **immediately**
- Each glyph is handled independently
- No full-text reflow, no frame drops

### ⏱ Adjustable Scramble Timing
- Control scramble speed (iterations per second)
- Optional **random extra scramble duration per glyph**
- Extra time is randomized **per character**
- Maximum additional time configurable (0–2 seconds)

### 🧠 Sequential Paste Mode
- Paste large blocks of text
- Characters appear **one by one**
- Each character becomes visible only when it starts scrambling

### 🧩 Typography Controls
- Font selection (Inter, Space Grotesk, IBM Plex Mono, JetBrains Mono)
- Base font size
- Scramble size multiplier
- Superscript scramble mode

### 🎨 Visual Options
- Random per-glyph colors
- Smooth Dark ↔ Light theme interpolation
- Clean, minimal UI

### 🟢 Matrix Mode
- Green “Matrix” color scheme
- Katakana + numeric scramble pool
- Monospace Japanese-style typography
- Can be combined with all other modes

### 🌍 Internationalization (i18n)
Fully translated UI (hardcoded, no external dependencies):

- Deutsch
- English
- Français
- Español
- Italiano
- Nederlands
- Polski
- Português
- Svenska
- Suomi
- Čeština
- Română
- Български
- Русский
- 中文（简体）
- हिन्दी
- 日本語

**Everything** is translated:
- Labels
- Section titles
- Sliders
- Buttons
- Placeholders
- Page title

---

## 🖥 Layout

- **Left panel:** all settings & controls
- **Right panel:**  
  - Output (top)  
  - Input (bottom)
- Both input and output are scrollable
- Full viewport height

---

## ⚙️ How It Works (Technical Overview)

- Each character is represented by its own `Glyph` instance
- Scrambling is driven by:
  - `requestAnimationFrame`
  - controlled time intervals (not tied to frame rate)
- No `setInterval` leaks
- Each glyph has:
  - its own timing
  - its own randomization state
  - its own lifecycle

Scramble duration logic:
base iterations + random(0 → maxExtraTime)
