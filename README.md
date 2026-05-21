# Timberborn Dashboard
 
A simple live dashboard for friends to watch the current weather state of my Timberborn colony and trigger fireworks.
 
## Starting the Tunnel (every session)
 
The game's API only runs locally, so a tunnel is needed to make it accessible from outside.
 
1. Open Command Prompt — press the Windows key, type `cmd`, press Enter
2. Run the following command:
   ```
   D:\Documents\05_Hobby\Timberborn\cloudflared-windows-amd64.exe tunnel --url http://localhost:8080
   ```
3. Wait ~10 seconds until a URL appears, e.g.:
   ```
   https://something-xyz-abc.trycloudflare.com
   ```
4. Copy that URL and paste it into `index.html`
   ```js
   base: 'https://something-xyz-abc.trycloudflare.com',
   ```
5. Commit the changes and wait ~2 minutes
6. Start the API in Timberborn
> The Command Prompt window must stay open while playing. The URL changes every time the tunnel is restarted.
