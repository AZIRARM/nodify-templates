# Nodify Simulator Health Template

A complete **IoT healthcare monitoring template** for [Nodify Headless CMS](https://github.com/AZIRARM/nodify). Simulate patients, track vital signs in real-time, and monitor alerts — no backend code required.

## 🚀 How to Use

1. **Download** the template JSON file:  
   👉 [Nodify-SIMULATOR_HEALTH.json](Nodify-SIMULATOR_HEALTH.json)

2. **Import into Nodify**:
   - Go to **Projects → Environments**.
   - Select your environment.
   - Click on the **import button** (⬇️ inside a circle).
   - Import the JSON file.

3. **Preview the Template**:
   - Navigate to the **Internet Of Things** node.
   - Open the **Health Simulator** content → click 👁️ to preview.
   - Open the **Health Dashboard** content → click 👁️ to see patient monitoring.

## 📦 Features

- 🏥 **Patient Simulator** — Add/remove patients with auto-generated vital signs
- 📊 **Health Dashboard** — Real-time monitoring with Chart.js graphs
- 🚨 **Smart Alerts** — Automatic warning/critical levels based on medical thresholds
- 📈 **History Charts** — Heart rate evolution over time
- 🗄️ **Nodify Datas API** — `POST /datas/`, `PUT /datas/id/{id}`, `GET /datas/contentCode/{code}`

## 🩺 Monitored Vital Signs

| Vital Sign | Unit | Normal Range | Warning | Critical |
|------------|------|--------------|---------|----------|
| Heart Rate | bpm | 60-100 | 50-120 | <40 or >140 |
| Blood Pressure (Systolic) | mmHg | 90-120 | 80-140 | <70 or >160 |
| Blood Pressure (Diastolic) | mmHg | 60-80 | 50-90 | <40 or >100 |
| Temperature | °C | 36.1-37.2 | 35.5-38.5 | <35 or >39.5 |
| SpO2 | % | 95-100 | 90-100 | <85 |

## 🛠️ How It Works

| Component | Role | Nodify API |
|-----------|------|------------|
| Health Simulator | Generates patient vitals, sends data | `POST /datas/` (first), `PUT /datas/id/{id}` (updates) |
| Health Dashboard | Displays all patients with charts | `GET /datas/contentCode/{code}` |

## 📁 Template Structure

```
Internet Of Things (node)
└── Health Tracker (node)
    ├── Health Simulator (content node) → HTML/CSS/JS
    └── Health Dashboard (content node) → HTML/CSS/JS + Chart.js
```

## 🔧 Prerequisites

- Nodify CMS installed (see [nodify repo](https://github.com/AZIRARM/nodify))
- Docker Compose setup with MongoDB and Redis

## 🎯 Alert Levels

| Level | Color | Behavior |
|-------|-------|----------|
| ✅ Normal | Green | Stable, no action needed |
| ⚠️ Warning | Orange | Monitor closely, pulse animation |
| 🚨 Critical | Red | Immediate attention, blinking animation |

## 📸 Preview

📸 *[Screenshot of health dashboard with patient cards and charts]*

## 🌐 Try the Live Demo

Test Nodify online:  
🔗 https://nodify-core.azirar.ovh

**Credentials:**  
- Username: `admin`  
- Password: `Admin13579++`

> ⚠️ Demo server accessible daily from 10:00 AM to 12:00 AM (UTC+1). Data may be reset.

## 📚 Related Resources

- [Nodify GitHub](https://github.com/AZIRARM/nodify) — Core CMS
- [Nodify Clients](https://github.com/AZIRARM/nodify-clients) — Python, Java, Node.js SDKs
- [More Templates](https://github.com/AZIRARM/nodify-templates) — Blog, E-commerce, Gallery, News

## 🤝 Contributing

Contributions welcome! Submit a pull request with your improvements.

## 📄 License

This template is licensed under the **MIT License**.  
Nodify CMS itself is licensed under CC BY-NC 4.0.

---

**Made with ❤️ by the Nodify Community**