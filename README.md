# Artemis — QR Scan Bridge

![Node.js](https://img.shields.io/badge/Node.js-18.x-green?logo=node.js)
![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-informational?logo=postgresql)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)

> A robust bridge system for serial QR scanners → Web dashboard + embedded devices.  
> Backend: **Express.js + Socket.IO + WebSocket + PostgreSQL**  
> Client: **Python Tkinter + Serial + WebSocket**

---

## 🚀 Features
- ✅ Store every QR scan in PostgreSQL with timestamp  
- ✅ Broadcast real-time scans to:
  - Web frontend (Socket.IO)
  - Embedded devices / Python clients (raw WebSocket)  
- ✅ REST API for fetching latest scans  
- ✅ Python Tkinter GUI client for serial QR readers  
- ✅ Auto reconnect for both server & client  

---

## 🏗 Architecture
| Component   | Tech Stack |
|-------------|------------|
| Backend     | Node.js (Express, Socket.IO, ws) |
| Database    | PostgreSQL |
| Client      | Python (Tkinter, pyserial, websocket-client) |
| Devices     | ESP32, USB QR Scanners |

![Architecture Diagram](docs/architecture.png)  
*(You can replace with your own diagram)*

---

## 📦 Backend (Express.js)

### Installation
```bash
git clone <your-repo-url>
cd <repo>
npm install
