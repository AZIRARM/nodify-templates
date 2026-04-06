# Nodify Phones Tracker Template

A complete **IoT tracking template** for [Nodify Headless CMS](https://github.com/AZIRARM/nodify). Track phones in real-time on a map — no backend code required.

## 🚀 How to Use

1. **Download** the template JSON file:  
   👉 [Nodify-PHONES_TRACKER.json](Nodify-PHONES_TRACKER.json)

2. **Import into Nodify**:
   - Go to **Projects → Environments**.
   - Select your environment.
   - Click on the **import button** (⬇️ inside a circle).
   - Import the JSON file.

3. **Preview the Template**:
   - Navigate to the **Phones Tracker** node.
   - Open the **Phone Simulator** content → click 👁️ to preview.
   - Open the **Dashboard** content → click 👁️ to see the map.

## 📦 Features

- 📱 **Phone Simulator** — HTML/JS page that captures GPS position and sends to Nodify
- 🗺️ **Live Dashboard** — Leaflet map showing all connected phones in real-time
- 🔄 **Auto-refresh** — Dashboard updates every 5 seconds
- 📍 **GPS tracking** — Uses browser geolocation API
- 🗄️ **Nodify Datas API** — `POST /datas/`, `PUT /datas/id/{id}`, `GET /datas/contentCode/{code}`

## 🛠️ How It Works

| Component | Role | Nodify API |
|-----------|------|------------|
| Phone Simulator | Captures GPS, sends position | `POST /datas/` (first), `PUT /datas/id/{id}` (updates) |
| Dashboard | Displays all phones on map | `GET /datas/contentCode/{code}` |

## 📁 Template Structure

```
Internet Of Things (node)
└── Phones Tracker (node)
    ├── Phone Simulator (content node) → HTML/CSS/JS
    └── Dashboard (content node) → HTML/CSS/JS + Leaflet map
```

## 🔧 Prerequisites

- Nodify CMS installed (see [nodify repo](https://github.com/AZIRARM/nodify))
- Docker Compose setup with MongoDB and Redis
- For GPS: HTTPS or localhost (browser requires secure context)

## 📸 Preview

📸 *[Screenshot of dashboard with phones on map]*

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