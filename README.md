# 🖥️ ESPHome LVGL HTML Simulator

A **browser-based simulator** for ESPHome displays using **LVGL** (Light and Versatile Graphics Library).  
Visualize the display output of your entire ESPHome configuration directly in your browser — **no physical hardware required!**

---

## ⚡ Key Features

- 🌐 **HTML-Based** — Runs entirely in the browser, platform-independent.  
- 📝 **Full ESPHome Simulation** — Paste your entire ESPHome configuration, and the simulator renders the LVGL display output automatically.  
- ⏱️ **Real-Time Rendering** — Displays LVGL widgets, layouts, and styles as they would appear on the actual ESP device.  
- 🔌 **Hardware-Free** — Perfect for testing, debugging, and designing display interfaces without physical hardware.  

---

## 🚀 Live Demo

👉 **[Try it now](https://6a043d09acce4f000807132e--funny-sawine-ed4fc8.netlify.app/)**  


---

## 🚀 How It Works

1. Copy your ESPHome configuration  
2. Paste it into the simulator  
3. Instantly preview your LVGL-based display UI in the browser

The following content serves solely as an example and is not used for commercial purposes; copyright remains with the creator.
<img width="800" height="396" alt="ezgif-6cee22afb2576a0c" src="https://github.com/user-attachments/assets/1a5c40e5-da17-443a-8e91-41a1524091b6" />

---

## 🎯 Who It's For

- ESPHome developers  
- LVGL UI designers  
- Makers & hobbyists  
- Anyone testing ESP display interfaces without hardware  

---

## 💡 Example Workflow

```yaml
lvgl:
  pages:
    - id: main_page
      widgets:
        - label:
            text: "Hello World"
