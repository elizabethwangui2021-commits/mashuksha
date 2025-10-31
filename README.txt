Deriv Simple Live Analysis — README

Files included:
- index.js                (server)
- package.json
- public/index.html
- public/app.js
- .env.example

How to run on CodeSandbox / Replit:
1. Create a new Node.js sandbox (or Repl).
2. Upload the files (or unzip the provided zip).
3. Add a secret/environment variable:
   KEY: DERIV_API_TOKEN
   VALUE: <your_deriv_api_token>
4. In the shell, run:
   npm install
   npm start
5. Open the preview link; the app will connect and stream ticks.
   - Choose a symbol (e.g., R_100) and press Start.
   - To stop streaming, press Stop.

Important security note:
- Do NOT paste your Deriv API token into public places. Use Secrets or a local .env file.
