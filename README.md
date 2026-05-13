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

The following content serves solely as an example; copyright remains with the creator.
<img width="800" height="450" alt="1" src="https://github.com/user-attachments/assets/423513a9-b5f0-4f4a-a1f8-07fc8ee5b4ff" />
<img width="800" height="450" alt="2" src="https://github.com/user-attachments/assets/ed058792-439a-4e8c-8f3e-537f47b15a99" />


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
