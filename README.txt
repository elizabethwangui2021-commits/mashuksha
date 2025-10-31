
import express from "express";
import path from "path";
import dotenv from "dotenv";
import { WebSocketServer } from "ws";
import { fileURLToPath } from "url";

dotenv.config();
const app = express();
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// Serve static files
app.use(express.static(path.join(__dirname, "public")));

const PORT = process.env.PORT || 3000;

// WebSocket setup
const wss = new WebSocketServer({ noServer: true });

// Deriv API connection
const DERIV_APP_ID = process.env.DERIV_APP_ID || "1089";
const DERIV_TOKEN = process.env.DERIV_API_TOKEN;

// Handle websocket upgrades
const server = app.listen(PORT, () => {
  console.log(`✅ Server running at http://localhost:${PORT}`);
});

server.on("upgrade", (req, socket, head) => {
  wss.handleUpgrade(req, socket, head, (ws) => {
    wss.emit("connection", ws, req);
  });
});

wss.on("connection", (ws) => {
  console.log("📡 Client connected");

  let derivSocket;

  ws.on("message", (message) => {
    const data = JSON.parse(message);

    if (data.action === "start") {
      const { symbol } = data;
      derivSocket = new WebSocket(`wss://ws.derivws.com/websockets/v3?app_id=${DERIV_APP_ID}`);

      derivSocket.onopen = () => {
        derivSocket.send(
          JSON.stringify({
            authorize: DERIV_TOKEN,
          })
        );

        derivSocket.send(
          JSON.stringify({
            ticks: symbol,
          })
        );
      };

      derivSocket.onmessage = (event) => {
        const msg = JSON.parse(event.data);
        if (msg.tick) {
          ws.send(JSON.stringify(msg.tick));
        }
      };
    }

    if (data.action === "stop" && derivSocket) {
      derivSocket.close();
      derivSocket = null;
      ws.send(JSON.stringify({ stopped: true }));
    }
  });

  ws.on("close", () => {
    console.log("❌ Client disconnected");
    if (derivSocket) derivSocket.close();
  });
});
{
  "name": "deriv-simple-live-analysis",
  "version": "1.0.0",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "dotenv": "^16.4.5",
    "express": "^4.18.2",
    "ws": "^8.13.0"
  }
}

DERIV_API_TOKEN=UFADYAHA7weGE0C
DERIV_APP_ID=1089

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Deriv Simple Live Analysis</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #0d1117;
      color: #e6edf3;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }
    input, button, select {
      margin: 5px;
      padding: 8px;
      border-radius: 5px;
      border: none;
    }
    button {
      background: #238636;
      color: white;
      cursor: pointer;
    }
    button:hover {
      background: #2ea043;
    }
    #output {
      margin-top: 20px;
      border: 1px solid #444;
      border-radius: 10px;
      padding: 10px;
      width: 300px;
      text-align: center;
      background: #161b22;
    }
  </style>
</head>
<body>
  <h1>📊 Deriv Simple Live Analysis</h1>
  <select id="symbol">
    <option value="R_100">R_100</option>
    <option value="R_50">R_50</option>
    <option value="R_25">R_25</option>
    <option value="R_10">R_10</option>
    <option value="R_75">R_75</option>
  </select>
  <button id="start">Start</button>
  <button id="stop">Stop</button>
  <div id="output">Waiting for data...</div>

  <script src="app.js"></script>
</body>
</htmlconst output = document.getElementById("output");
const startBtn = document.getElementById("start");
const stopBtn = document.getElementById("stop");
const symbolSelect = document.getElementById("symbol");

const ws = new WebSocket(`ws://${window.location.host}`);

startBtn.onclick = () => {
  const symbol = symbolSelect.value;
  ws.send(JSON.stringify({ action: "start", symbol }));
  output.textContent = `Streaming ${symbol}...`;
};

stopBtn.onclick = () => {
  ws.send(JSON.stringify({ action: "stop" }));
  output.textContent = "Stopped.";
};

ws.onmessage = (event) => {
  const tick = JSON.parse(event.data);
  if (tick.quote) {
    output.textContent = `${tick.symbol} → ${tick.quote}`;
  }
};
>
