# 🖥️ ESPHome LVGL Simulator

**Build and preview ESPHome LVGL dashboards without constantly flashing your ESP32.**

A browser-based simulator for ESPHome displays using **LVGL** (Light and Versatile Graphics Library).  
Paste your ESPHome config and instantly see how your display will look — **no hardware required.**

---

## 🚀 How It Works

1. Copy your ESPHome YAML configuration
2. Paste it into the simulator editor
3. Click **Render** — your display appears instantly

<img width="800" height="450" alt="1" src="https://github.com/user-attachments/assets/423513a9-b5f0-4f4a-a1f8-07fc8ee5b4ff" />
<img width="800" height="450" alt="2" src="https://github.com/user-attachments/assets/ed058792-439a-4e8c-8f3e-537f47b15a99" />


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

For MDI icons offline: place `materialdesignicons-webfont.woff2` or `.ttf` in a `fonts/` subfolder next to the HTML file. Both formats are supported — `woff2` is preferred (smaller), `ttf` is used as fallback.

---

## 📦 Supported ESPHome Features
 
| Feature | Supported |
|---|:---:|
| Substitutions | ✅ |
| Named colors (`color:` section) | ✅ |
| Style definitions & theme | ✅ |
| Dynamic font system (MDI, Meteocons, gfonts://, local .ttf/.woff2) | ✅ |
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

## 💡 Example Workflow

<details>
<summary>Example</summary>
  
```yaml
substitutions:
  name: "guition-4848s040"
  friendly_name: "Wohnzimmer Panel"
  room: "Wohnzimmer"

esphome:
  name: ${name}
  friendly_name: ${friendly_name}

esp32:
  board: esp32-s3-devkitc-1
  variant: esp32s3
  framework:
    type: esp-idf
    sdkconfig_options:
      CONFIG_ESP32S3_DEFAULT_CPU_FREQ_240: y
      CONFIG_ESP32S3_DATA_CACHE_64KB: y
      CONFIG_SPIRAM_FETCH_INSTRUCTIONS: y
      CONFIG_SPIRAM_RODATA: y

psram:
  mode: octal
  speed: 80MHz

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

logger:
api:
ota:
  platform: esphome

i2c:
  sda: GPIO8
  scl: GPIO9
  scan: true

display:
  - platform: rpi_dpi_rgb
    id: my_display
    update_interval: never
    auto_clear_enabled: false
    color_order: RGB
    pclk_frequency: 16MHz
    dimensions:
      width: 480
      height: 480
    de_pin: 5
    hsync_pin: 46
    vsync_pin: 3
    pclk_pin: 7
    data_pins:
      red:   [1, 2, 42, 41, 40]
      green: [39, 0, 45, 48, 47, 21]
      blue:  [14, 38, 18, 17, 10]

touchscreen:
  platform: gt911
  address: 0x5D
  id: my_touch
  interrupt_pin: 4

time:
  - platform: homeassistant
    id: ha_time

sensor:
  - platform: homeassistant
    id: temp_wohnzimmer
    entity_id: sensor.wohnzimmer_temperature
  - platform: homeassistant
    id: hum_wohnzimmer
    entity_id: sensor.wohnzimmer_humidity
  - platform: homeassistant
    id: power_total
    entity_id: sensor.stromverbrauch_gesamt
  - platform: homeassistant
    id: solar_power
    entity_id: sensor.solar_leistung
  - platform: wifi_signal
    id: wifi_db
    update_interval: 60s

binary_sensor:
  - platform: homeassistant
    id: licht_wohnzimmer
    entity_id: light.wohnzimmer_decke
    on_state:
      then:
        - lvgl.widget.update:
            id: btn_licht_wz
            state:
              checked: !lambda return x;
  - platform: homeassistant
    id: licht_stehlampe
    entity_id: light.stehlampe
    on_state:
      then:
        - lvgl.widget.update:
            id: btn_stehlampe
            state:
              checked: !lambda return x;
  - platform: homeassistant
    id: tv_state
    entity_id: media_player.wohnzimmer_tv
    on_state:
      then:
        - lvgl.widget.update:
            id: btn_tv
            state:
              checked: !lambda return x;

font:
  - file: "gfonts://Roboto"
    id: roboto_12
    size: 12
    bpp: 4
    extras:
      - file: 'https://github.com/Templarian/MaterialDesign-Webfont/raw/v7.4.47/fonts/materialdesignicons-webfont.ttf'
        glyphs: [
          "\U000F0335", "\U000F0336",
          "\U000F040A", "\U000F0502",
          "\U000F0F00", "\U000F059C",
          "\U000F06D8", "\U000F0140",
          "\U000F0141", "\U000F06E8",
          "\U000F050F", "\U000F0425",
          "\U000F0769", "\U000F007A",
          "\U000F1B7C", "\U000F0238",
        ]
  - file: "gfonts://Roboto"
    id: roboto_18
    size: 18
    bpp: 4
  - file: "gfonts://Roboto"
    id: roboto_28
    size: 28
    bpp: 4
  - file: "gfonts://Roboto"
    id: roboto_48
    size: 48
    bpp: 4
  - file: 'https://github.com/Templarian/MaterialDesign-Webfont/raw/v7.4.47/fonts/materialdesignicons-webfont.ttf'
    id: mdi_36
    size: 36
    bpp: 4
    glyphs: [
      "\U000F0335", "\U000F0336",
      "\U000F040A", "\U000F0502",
      "\U000F0F00", "\U000F059C",
      "\U000F06D8", "\U000F0140",
      "\U000F0141", "\U000F06E8",
      "\U000F050F", "\U000F0425",
      "\U000F0769", "\U000F007A",
      "\U000F1B7C", "\U000F0238",
    ]
  - file: 'https://github.com/Templarian/MaterialDesign-Webfont/raw/v7.4.47/fonts/materialdesignicons-webfont.ttf'
    id: mdi_56
    size: 56
    bpp: 4
    glyphs: [
      "\U000F0335", "\U000F0336",
      "\U000F040A", "\U000F0502",
      "\U000F0F00", "\U000F059C",
      "\U000F06D8", "\U000F0140",
    ]

color:
  - id: col_blue
    hex: "2F8CD8"
  - id: col_blue_dark
    hex: "005782"
  - id: col_orange
    hex: "FF8C00"
  - id: col_green
    hex: "00A550"
  - id: col_red
    hex: "E53935"
  - id: col_bg
    hex: "1A1A2E"
  - id: col_card
    hex: "16213E"
  - id: col_text
    hex: "E0E0E0"
  - id: col_text_dim
    hex: "808080"
  - id: col_yellow
    hex: "FFD700"

lvgl:
  displays:
    - my_display
  touchscreens:
    - my_touch

  theme:
    button:
      radius: 12
      width: 100
      height: 90
      bg_color: col_card
      text_color: col_text_dim
      border_width: 1
      border_color: 0x303050
      shadow_width: 0
      pad_all: 8
      checked:
        bg_color: col_blue
        text_color: col_text
        border_color: col_blue

  style_definitions:
    - id: card_style
      radius: 10
      bg_color: col_card
      bg_opa: COVER
      border_width: 1
      border_color: 0x303050
      pad_all: 10
    - id: header_style
      bg_color: col_blue
      bg_grad_color: col_blue_dark
      bg_grad_dir: VER
      bg_opa: COVER
      radius: 0
      pad_all: 0
      border_width: 0
      text_color: 0xFFFFFF
      height: 48
      width: 100%
    - id: value_big
      text_font: roboto_48
      text_color: col_text
    - id: label_small
      text_font: roboto_12
      text_color: col_text_dim

  top_layer:
    widgets:
      - obj:
          align: TOP_MID
          styles: header_style
          widgets:
            - label:
                id: lbl_time
                align: LEFT_MID
                x: 12
                text_font: roboto_18
                text_color: 0xFFFFFF
                text: "00:00"
            - label:
                text: "Wohnzimmer Panel"
                align: CENTER
                text_font: roboto_18
                text_color: 0xFFFFFF
            - label:
                id: lbl_wifi
                align: RIGHT_MID
                x: -12
                text_font: mdi_36
                text_color: 0xFFFFFF
                text: "\U000F06E8"
      - buttonmatrix:
          align: BOTTOM_MID
          styles: header_style
          pad_all: 0
          outline_width: 0
          items:
            styles: header_style
          rows:
            - buttons:
              - id: nav_prev
                text: "\U000F0141"
                on_press:
                  then:
                    lvgl.page.previous:
              - id: nav_home
                text: "\U000F0769"
                on_press:
                  then:
                    lvgl.page.show: page_home
              - id: nav_next
                text: "\U000F0140"
                on_press:
                  then:
                    lvgl.page.next:

  pages:
    # ── SEITE 1: Übersicht ──────────────────────────────────────────
    - id: page_home
      bg_color: col_bg
      bg_opa: COVER
      widgets:
        # Temperatur-Karte oben links
        - obj:
            x: 8
            y: 56
            width: 220
            height: 130
            styles: card_style
            widgets:
              - label:
                  x: 8
                  y: 8
                  text: "\U000F050F"
                  text_font: mdi_36
                  text_color: col_orange
              - label:
                  id: lbl_temp
                  x: 54
                  y: 10
                  text_font: roboto_48
                  text_color: col_text
                  text: "21.5°"
              - label:
                  x: 8
                  y: 75
                  text: "Temperatur Innen"
                  text_font: roboto_12
                  text_color: col_text_dim
              - label:
                  id: lbl_hum
                  x: 8
                  y: 95
                  text: "Luftfeuchtigkeit: 52%"
                  text_font: roboto_12
                  text_color: col_text_dim

        # Solar-Karte oben rechts
        - obj:
            x: 248
            y: 56
            width: 224
            height: 130
            styles: card_style
            widgets:
              - label:
                  x: 8
                  y: 8
                  text: "\U000F059C"
                  text_font: mdi_36
                  text_color: col_yellow
              - label:
                  id: lbl_solar
                  x: 54
                  y: 10
                  text_font: roboto_48
                  text_color: col_yellow
                  text: "3.4kW"
              - label:
                  x: 8
                  y: 75
                  text: "Solar Leistung"
                  text_font: roboto_12
                  text_color: col_text_dim
              - bar:
                  id: bar_solar
                  x: 8
                  y: 98
                  width: 200
                  height: 12
                  min_value: 0
                  max_value: 8000
                  value: 3400
                  bg_color: 0x303050
                  radius: 6
                  indicator:
                    bg_color: col_yellow

        # Stromverbrauch Mitte links
        - obj:
            x: 8
            y: 196
            width: 220
            height: 130
            styles: card_style
            widgets:
              - label:
                  x: 8
                  y: 8
                  text: "\U000F06D8"
                  text_font: mdi_36
                  text_color: col_red
              - label:
                  id: lbl_power
                  x: 54
                  y: 10
                  text_font: roboto_48
                  text_color: col_red
                  text: "1.2kW"
              - label:
                  x: 8
                  y: 75
                  text: "Stromverbrauch"
                  text_font: roboto_12
                  text_color: col_text_dim
              - bar:
                  id: bar_power
                  x: 8
                  y: 98
                  width: 200
                  height: 12
                  min_value: 0
                  max_value: 6000
                  value: 1200
                  bg_color: 0x303050
                  radius: 6
                  indicator:
                    bg_color: col_red

        # Schnellzugriff Licht rechts
        - obj:
            x: 248
            y: 196
            width: 224
            height: 130
            styles: card_style
            layout:
              type: flex
              flex_flow: ROW_WRAP
              flex_align_main: SPACE_BETWEEN
              flex_align_cross: CENTER
            widgets:
              - label:
                  text: "Licht"
                  text_font: roboto_12
                  text_color: col_text_dim
                  width: 224
              - btn:
                  id: btn_licht_wz
                  checkable: true
                  width: 95
                  height: 75
                  widgets:
                    - label:
                        align: top_mid
                        text: "\U000F0769"
                        text_font: mdi_36
                    - label:
                        align: bottom_mid
                        text: "Decke"
                        text_font: roboto_12
              - btn:
                  id: btn_stehlampe
                  checkable: true
                  width: 95
                  height: 75
                  widgets:
                    - label:
                        align: top_mid
                        text: "\U000F0335"
                        text_font: mdi_36
                    - label:
                        align: bottom_mid
                        text: "Stehlampe"
                        text_font: roboto_12

        # TV / Unterhaltung unten
        - obj:
            x: 8
            y: 336
            width: 464
            height: 96
            styles: card_style
            layout:
              type: flex
              flex_flow: ROW
              flex_align_main: SPACE_BETWEEN
              flex_align_cross: CENTER
            widgets:
              - btn:
                  id: btn_tv
                  checkable: true
                  width: 100
                  height: 75
                  widgets:
                    - label:
                        align: top_mid
                        text: "\U000F0502"
                        text_font: mdi_36
                    - label:
                        align: bottom_mid
                        text: "TV"
                        text_font: roboto_12
              - btn:
                  id: btn_musik
                  checkable: true
                  width: 100
                  height: 75
                  widgets:
                    - label:
                        align: top_mid
                        text: "\U000F0F00"
                        text_font: mdi_36
                    - label:
                        align: bottom_mid
                        text: "Musik"
                        text_font: roboto_12
              - btn:
                  id: btn_roller
                  checkable: true
                  width: 100
                  height: 75
                  widgets:
                    - label:
                        align: top_mid
                        text: "\U000F0238"
                        text_font: mdi_36
                    - label:
                        align: bottom_mid
                        text: "Rollo"
                        text_font: roboto_12
              - btn:
                  id: btn_alarm
                  checkable: true
                  width: 100
                  height: 75
                  widgets:
                    - label:
                        align: top_mid
                        text: "\U000F1B7C"
                        text_font: mdi_36
                    - label:
                        align: bottom_mid
                        text: "Alarm"
                        text_font: roboto_12

    # ── SEITE 2: Klima ──────────────────────────────────────────────
    - id: page_klima
      bg_color: col_bg
      bg_opa: COVER
      widgets:
        - label:
            x: 20
            y: 60
            text: "Klimasteuerung"
            text_font: roboto_28
            text_color: col_text
        # Großes Thermometer
        - arc:
            id: arc_temp
            x: 80
            y: 100
            width: 240
            height: 240
            align: CENTER
            arc_width: 20
            start_angle: 135
            end_angle: 405
            value: 215
            min_value: 0
            max_value: 400
            bg_color: 0x303050
            indicator:
              arc_width: 20
              bg_color: col_orange
        - label:
            align: CENTER
            y: -20
            text_font: roboto_48
            text_color: col_text
            text: "21.5°C"
        - label:
            align: CENTER
            y: 30
            text_font: roboto_18
            text_color: col_text_dim
            text: "Soll: 22°C"
        # Thermostat Buttons
        - obj:
            x: 8
            y: 370
            width: 464
            height: 70
            bg_opa: 0
            border_width: 0
            layout:
              type: flex
              flex_flow: ROW
              flex_align_main: SPACE_EVENLY
              flex_align_cross: CENTER
            widgets:
              - btn:
                  width: 80
                  height: 60
                  checkable: true
                  widgets:
                    - label:
                        align: CENTER
                        text: "Kühlen"
                        text_font: roboto_12
              - btn:
                  width: 80
                  height: 60
                  checkable: true
                  widgets:
                    - label:
                        align: CENTER
                        text: "Heizen"
                        text_font: roboto_12
              - btn:
                  width: 80
                  height: 60
                  checkable: true
                  widgets:
                    - label:
                        align: CENTER
                        text: "Auto"
                        text_font: roboto_12
              - btn:
                  width: 80
                  height: 60
                  widgets:
                    - label:
                        align: CENTER
                        text: "Aus"
                        text_font: roboto_12

    # ── SEITE 3: Solar & Energie ─────────────────────────────────────
    - id: page_energie
      bg_color: col_bg
      bg_opa: COVER
      widgets:
        - label:
            x: 20
            y: 60
            text: "Energie"
            text_font: roboto_28
            text_color: col_text
        # Solar Meter
        - meter:
            x: 20
            y: 100
            width: 200
            height: 200
            bg_color: col_card
            bg_opa: COVER
            border_width: 1
            border_color: 0x303050
            radius: 10
            scales:
              - range_from: 0
                range_to: 8000
                angle_range: 240
                rotation: 150
                ticks:
                  width: 2
                  count: 9
                  length: 10
                  color: col_text_dim
                  major:
                    stride: 4
                    width: 3
                    length: 15
                    color: col_text
                    label_gap: 8
                indicators:
                  - arc:
                      color: col_green
                      r_mod: 10
                      width: 15
                      start_value: 0
                      end_value: 4000
                  - arc:
                      color: col_yellow
                      r_mod: 10
                      width: 15
                      start_value: 4000
                      end_value: 6000
                  - arc:
                      color: col_orange
                      r_mod: 10
                      width: 15
                      start_value: 6000
                      end_value: 8000
                  - line:
                      id: needle_solar
                      width: 3
                      color: col_yellow
                      r_mod: -4
                      value: 3400
            widgets:
              - label:
                  align: CENTER
                  y: 30
                  text: "Solar"
                  text_font: roboto_12
                  text_color: col_text_dim
              - label:
                  id: lbl_solar_meter
                  align: CENTER
                  y: 10
                  text: "3.4 kW"
                  text_font: roboto_28
                  text_color: col_yellow

        # Verbrauchsübersicht rechts
        - obj:
            x: 250
            y: 100
            width: 220
            height: 200
            styles: card_style
            widgets:
              - label:
                  x: 10
                  y: 10
                  text: "Heute"
                  text_font: roboto_18
                  text_color: col_text
              - label:
                  x: 10
                  y: 40
                  text: "\U000F059C Solar:"
                  text_font: roboto_12
                  text_color: col_yellow
              - label:
                  x: 120
                  y: 40
                  text: "18.4 kWh"
                  text_font: roboto_12
                  text_color: col_text
              - label:
                  x: 10
                  y: 68
                  text: "\U000F06D8 Verbrauch:"
                  text_font: roboto_12
                  text_color: col_red
              - label:
                  x: 120
                  y: 68
                  text: "9.2 kWh"
                  text_font: roboto_12
                  text_color: col_text
              - label:
                  x: 10
                  y: 96
                  text: "\U000F0140 Einspeisung:"
                  text_font: roboto_12
                  text_color: col_green
              - label:
                  x: 120
                  y: 96
                  text: "9.2 kWh"
                  text_font: roboto_12
                  text_color: col_text
              - line:
                  x: 10
                  y: 118
                  width: 195
                  height: 2
                  line_color: 0x303050
                  line_width: 1
              - label:
                  x: 10
                  y: 128
                  text: "Autarkie heute:"
                  text_font: roboto_12
                  text_color: col_text_dim
              - bar:
                  x: 10
                  y: 148
                  width: 195
                  height: 16
                  min_value: 0
                  max_value: 100
                  value: 68
                  bg_color: 0x303050
                  radius: 8
                  indicator:
                    bg_color: col_green
              - label:
                  x: 80
                  y: 148
                  text: "68%"
                  text_font: roboto_12
                  text_color: col_text

        # Stundenverbrauch-Balken
        - obj:
            x: 8
            y: 320
            width: 464
            height: 100
            styles: card_style
            layout:
              type: GRID
              grid_columns: [FR(1), FR(1), FR(1), FR(1), FR(1), FR(1), FR(1), FR(1)]
              grid_rows: [FR(3), FR(1)]
            widgets:
              - bar:
                  grid_cell_column_pos: 0
                  grid_cell_row_pos: 0
                  grid_cell_y_align: STRETCH
                  width: 40
                  height: 60
                  min_value: 0
                  max_value: 100
                  value: 45
                  bg_color: 0x303050
                  indicator:
                    bg_color: col_yellow
              - bar:
                  grid_cell_column_pos: 1
                  grid_cell_row_pos: 0
                  grid_cell_y_align: STRETCH
                  width: 40
                  height: 60
                  min_value: 0
                  max_value: 100
                  value: 72
                  bg_color: 0x303050
                  indicator:
                    bg_color: col_yellow
              - bar:
                  grid_cell_column_pos: 2
                  grid_cell_row_pos: 0
                  grid_cell_y_align: STRETCH
                  width: 40
                  height: 60
                  min_value: 0
                  max_value: 100
                  value: 88
                  bg_color: 0x303050
                  indicator:
                    bg_color: col_yellow
              - bar:
                  grid_cell_column_pos: 3
                  grid_cell_row_pos: 0
                  grid_cell_y_align: STRETCH
                  width: 40
                  height: 60
                  min_value: 0
                  max_value: 100
                  value: 95
                  bg_color: 0x303050
                  indicator:
                    bg_color: col_orange
              - bar:
                  grid_cell_column_pos: 4
                  grid_cell_row_pos: 0
                  grid_cell_y_align: STRETCH
                  width: 40
                  height: 60
                  min_value: 0
                  max_value: 100
                  value: 83
                  bg_color: 0x303050
                  indicator:
                    bg_color: col_yellow
              - bar:
                  grid_cell_column_pos: 5
                  grid_cell_row_pos: 0
                  grid_cell_y_align: STRETCH
                  width: 40
                  height: 60
                  min_value: 0
                  max_value: 100
                  value: 62
                  bg_color: 0x303050
                  indicator:
                    bg_color: col_yellow
              - bar:
                  grid_cell_column_pos: 6
                  grid_cell_row_pos: 0
                  grid_cell_y_align: STRETCH
                  width: 40
                  height: 60
                  min_value: 0
                  max_value: 100
                  value: 41
                  bg_color: 0x303050
                  indicator:
                    bg_color: col_green
              - bar:
                  grid_cell_column_pos: 7
                  grid_cell_row_pos: 0
                  grid_cell_y_align: STRETCH
                  width: 40
                  height: 60
                  min_value: 0
                  max_value: 100
                  value: 28
                  bg_color: 0x303050
                  indicator:
                    bg_color: col_green
</details>

---

## 📄 License

MIT — free to use, modify and distribute.  
Example configs and screenshots remain the property of their respective creators.
