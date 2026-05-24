# 🏠 Smart Home Rendang

A minimal IoT smart home dashboard — live on **GitHub Pages**.  
Controls lighting (PWM dimmer), gate servo, and live CCTV stream via MQTT over WebSocket.

🔗 **Live Demo:** `https://jowick-dem.github.io/TestRepoIOT`
**Documentation :** `https://rendangiotdokumentasi.netlify.app/`
**Main Repository :**  `https://github.com/jowick-dem/TestRepoIOT`

---

## 📁 Project Structure

```
Smart_Home_Rendang/               ← GitHub repo root
├── index.html                    ← Dashboard (served by GitHub Pages)
├── assets/
│   ├── css/style.css             ← Minimalist dark theme
│   └── js/app.js                 ← MQTT logic + UI controller
├── firmware/
│   ├── main.ino                  ← ESP32: lights, gate, temperature
│   ├── cam_module.ino            ← ESP32-CAM: MJPEG stream + IP broadcast
│   └── config.h                  ← ⚠ Fill in credentials (never commit this)
├── docs/
│   └── wiring.md                 ← Hardware pin & wiring reference
├── .gitignore
└── README.md
```

---

## 🚀 Deploy to GitHub Pages

1. Push this repo to GitHub
2. Go to **Settings → Pages**
3. Set source to `main` branch, root folder `/`
4. Your dashboard will be live at `https://<username>.github.io/<repo-name>`

> GitHub Pages serves `index.html` from the root automatically — no configuration needed.

---

## 🔌 Hardware

| Component | Role |
|---|---|
| ESP32 DevKit | Main controller (WiFi, MQTT, PWM dimming) |
| ESP32-CAM (AI Thinker) | MJPEG video stream server |
| DHT22 | Temperature sensor |
| 4× MOSFET/LED driver | Dimmable light channels (0–255 PWM) |
| Servo motor | Gate open/close |

---

## 📡 MQTT Topics

| Topic | Direction | Payload |
|---|---|---|
| `proyekiot/sensor/suhu` | ESP32 → Web | Temperature float e.g. `29.5` |
| `proyekiot/ipcam` | CAM → Web | Local IP e.g. `192.168.1.42` |
| `proyekiot/lampu/teras` | Web → ESP32 | Brightness `0`–`255` |
| `proyekiot/lampu/tamu` | Web → ESP32 | Brightness `0`–`255` |
| `proyekiot/lampu/tidur` | Web → ESP32 | Brightness `0`–`255` |
| `proyekiot/lampu/dapur` | Web → ESP32 | Brightness `0`–`255` |
| `proyekiot/gerbang` | Web → ESP32 | `BUKA` or `TUTUP` |

All messages use `retained: true` so state survives reconnects.

---

## ⚙️ Firmware Setup

1. Copy the template and fill in your credentials:
   ```bash
   cp firmware/config.example.h firmware/config.h
   ```
   Then edit `config.h`:
   ```c
   #define WIFI_SSID  "your_wifi"
   #define WIFI_PASS  "your_password"
   ```
   > `config.h` is gitignored — credentials stay local. `config.example.h` is the safe template tracked by git.
2. Upload `main.ino` to **ESP32 DevKit**
3. Upload `cam_module.ino` to **ESP32-CAM** (board: AI Thinker ESP32-CAM)
4. Install required Arduino libraries:
   - `PubSubClient` by Nick O'Leary
   - `DHT sensor library` by Adafruit
   - `ESP32Servo`

---

## ⚠️ Security Notes

- Default config uses the **public** EMQX broker — anyone knowing your topic names can control your devices
- For production: use a **private broker** (Mosquitto, HiveMQ Cloud) with username/password auth
- The CCTV stream runs over plain HTTP — do not expose it outside your local network without a reverse proxy + TLS
- Add `firmware/config.h` to `.gitignore` and **never commit credentials**

---

## 🎨 Tech Stack

| Layer | Tech |
|---|---|
| Frontend | Vanilla HTML · CSS · JavaScript |
| Fonts | Syne + DM Mono (Google Fonts) |
| MQTT Client | Paho MQTT over WebSocket SSL (port 8084) |
| Hosting | GitHub Pages |
| Firmware | Arduino C++ · ESP32 framework |
| Protocol | MQTT with retained messages |
