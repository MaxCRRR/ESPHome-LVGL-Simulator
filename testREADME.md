# 🖥️ ESPHome LVGL Simulator

**Build and preview ESPHome LVGL dashboards without constantly flashing your ESP32.**

A browser-based simulator for ESPHome displays using **LVGL** (Light and Versatile Graphics Library).  
Paste your ESPHome config and instantly see how your display will look — **no hardware required.**

---

## 🚀 How It Works

1. Copy your ESPHome YAML configuration
2. Paste it into the simulator editor
3. Click **Render** — your display appears instantly

The simulator parses the entire config dynamically — substitutions, fonts, colors, layouts, pages and navigation all work out of the box.

---

## ✨ Features

- **Fully dynamic** — no hardcoded values, everything comes from your config
- **Multi-page** with working navigation (prev / next / home / page.show)
- **Flex & Grid layouts** — ROW, COLUMN, WRAP, FR units
- **All major widgets** — label, button, bar, slider, arc, meter, switch, checkbox, dropdown, roller, buttonmatrix, image, line, led, spinner
- **Theme & style definitions** parsed automatically
- **MDI Icons** via CDN or local font file
- **Google Fonts** via `gfonts://`
- **Named colors** from `color:` section
- **Substitutions** (`$var`, `${var}`)
- **`!lambda` placeholders** — shown as `~sensor_id~`
- **Random dummy values** for bars, sliders, arcs and meters on every render
- **Button press simulation** — colors respond to mouse events as defined in your config

---

## 🤖 AI Config Analysis *(optional)*

After every render, the simulator can automatically analyze your config using Claude AI and report potential issues directly below the display — missing fonts, invalid references, layout problems and more.

This feature requires an **Anthropic API key** (`sk-ant-...`), entered once in the **🤖 AI Key** field in the toolbar. The key is stored locally in your browser and never leaves your machine except to call the Anthropic API directly.

> **Note:** Getting an API key at [console.anthropic.com](https://console.anthropic.com) requires a credit card, even for free-tier usage. Without a key, the simulator works fully — AI analysis is simply skipped.

---

## 🌐 Running the Simulator

### Option A — GitHub Pages *(no install)*
Open directly in your browser — no setup needed.  
> MDI icons load from CDN automatically when online.  
> AI analysis is not available on GitHub Pages due to browser CORS restrictions.

### Option B — Local Web Server *(recommended for full feature support)*
```bash
cd /path/to/simulator
python -m http.server 8080
```
Then open: `http://localhost:8080/index.html`

For MDI icons offline: place `materialdesignicons-webfont.woff2` in a `fonts/` subfolder next to the HTML file.

---

## 📦 Supported ESPHome Features

| Feature | Supported |
|---|:---:|
| Substitutions | ✅ |
| Named colors (`color:` section) | ✅ |
| Style definitions & theme | ✅ |
| Dynamic font system (MDI, Meteocons, gfonts://, local) | ✅ |
| Flex layout (ROW / COLUMN / WRAP) | ✅ |
| Grid layout (FR / px) | ✅ |
| Top layer | ✅ |
| Buttonmatrix + navigation | ✅ |
| Script resolution (`page_move_prev/next`) | ✅ |
| Meter with scales, ticks, indicators | ✅ |
| Arc with start/end angle | ✅ |
| `align_to:` references | ✅ |
| `!lambda` placeholders | ✅ |
| Random dummy values on render | ✅ |
| AI config analysis | ✅ *(API key required)* |

---

## 📄 License

MIT — free to use, modify and distribute.  
Example configs and screenshots remain the property of their respective creators.
