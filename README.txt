  Glitch package - Deriv Live Signals + Chart


  How to deploy on Glitch (mobile):

1. Open https://glitch.com on your phone and sign in.
2. Create a new project -> "New Project" -> "hello-express".
3. Open Tools -> Import from Zip -> Upload the ZIP you downloaded from this chat.
4. After upload, go to the .env file in the Glitch editor and add:
   DERIV_API_TOKEN=your_deriv_api_token_here
5. Glitch will install dependencies and start the app automatically.
6. Your live site URL will be visible at the top-left or under "Share" -> "Live Site".

Notes:
- The server automatically subscribes to R_100 by default and will proxy ticks and computed signals to the browser.
- For best results, add your DERIV_API_TOKEN in the .env to authorize the connection.
- Don't share your API token publicly.
