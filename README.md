# Artemis — QR Scan Bridge (Express + Socket.IO + WebSocket + Python Serial Client)

A small, robust system to bridge hardware QR scanners (over serial) to a web dashboard and other realtime clients.
The backend is an Express.js server that exposes a REST API, Socket.IO for frontend clients, and a raw WebSocket endpoint for embedded devices (ESP32, Python clients). A Python Tkinter app listens to serial ports (QR scanner / reader), reads User ID and QR Code, and forwards scans to the backend via WebSocket.

Short description (one-line)

Bridge serial QR scanners to web and embedded clients in real-time — stores scans in PostgreSQL and broadcasts them via Socket.IO and raw WebSocket.

Features

Store every QR scan in PostgreSQL (timestamped).

Broadcast incoming scans to:

Socket.IO clients (web dashboard / frontend)

Raw WebSocket clients (ESP32, Python desktop)

Lightweight REST API for fetching recent scans.

Python Tkinter client that reads serial devices and forwards scans over WebSocket.

Simple, resilient reconnect logic in both server and clients.

Architecture overview

Express.js server

REST: GET /api.artemis/get — returns latest QR scans.

Socket.IO: path /api.artemis/socket — for web frontends.

Raw WebSocket: path /api.artemis/ws — for embedded devices or plain WS clients.

Shared broadcastScan() function saves to DB, then emits to both Socket.IO and WS clients.

Python client (Tkinter)

Reads serial lines from QR readers.

Extracts User ID: and QR Code: lines.

Sends { "qr_code": "...", "user_id": "..." } as JSON to ws://<server>:<port>/api.artemis/ws.

Optional GUI shows most recent scans and a button to open the web dashboard.

Quickstart — Backend (Node.js)

Clone repo and install:

git clone <your-repo-url>
cd <repo>
npm install


Environment variables (example .env):

PORT=3000
DATABASE_URL=postgres://user:password@localhost:5432/yourdb


Ensure PostgreSQL table exists (example SQL):

CREATE TABLE IF NOT EXISTS public.qr_control (
  id SERIAL PRIMARY KEY,
  qr_code TEXT NOT NULL,
  user_id TEXT,
  date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);


Start server:

node index.js
# or with nodemon
npx nodemon index.js


Endpoints / messages:

REST: GET http://localhost:3000/api.artemis/get

Socket.IO path: /api.artemis/socket (Socket.IO client connect needs socketio_path='/api.artemis/socket')

Raw WebSocket path: ws://localhost:3000/api.artemis/ws

Quickstart — Python serial client

Install Python deps:

pip install pyserial websocket-client
# plus Tkinter (usually in stdlib); on Debian/Ubuntu:
sudo apt-get install python3-tk


Configure serial ports and server address in the script (constants at top):

SERIAL_PORT = '/dev/ttyUSB0'
SERIAL_PORT1 = '/dev/ttyUSB1'
BAUD_RATE = 115200
WS_URL = "ws://10.0.108.10:99/api.artemis/ws"


Run:

python artemis_client.py


The client:

Watches serial lines for User ID: and QR Code:.

Sends JSON messages to server: {"qr_code": "...", "user_id": "..."}.

Has a small Tkinter window listing recent scans.

Example scan JSON (sent by client)
{
  "qr_code": "ABC123456",
  "user_id": "user-01",
  "date": "2025-09-17T01:23:45.678Z" // added by server when stored
}

Notes & best practices

CORS: Server currently allows origin: "*". For production, restrict origins to your frontend domain.

Security: Add authentication/authorization if scans are sensitive. Consider TLS (wss/https).

DB safety: Use parameterized queries (the server code already does) and proper migrations.

Reconnect: Client reconnection loops are implemented — tune backoff strategy for production.

Duplicates: Python client deduplicates consecutive identical QR codes; adjust logic if you need a different dedupe policy.

Development tips

Use socket.io-client in the browser to connect to the Socket.IO path:

const socket = io("http://localhost:3000", {
  path: "/api.artemis/socket"
});
socket.on("scan", payload => { console.log(payload); });


ESP32 and microcontrollers typically prefer raw WebSocket (/api.artemis/ws) for simplicity.

To test locally, tools like websocat or browser-based WS clients are handy:

websocat ws://localhost:3000/api.artemis/ws
