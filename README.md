# Enrique Shop Assistant Prototype

Enrique is a prototype shop dashboard for:

- phone photo upload
- live phone camera snapshots
- auto-label workflow for shop items
- inventory/photo logging
- invoice styling
- multiple billing entities
- security-camera planning
- customer/job documentation

## Quick Start

Open `index.html` in a browser for a basic local preview.

For phone upload/camera access, use a real web link instead of `file://`.

## Option 1: GitHub Pages Phone Link

This is the best simple way to get a phone-friendly HTTPS link.

1. Push this folder to GitHub.
2. Open the repo on GitHub.
3. Go to `Settings` -> `Pages`.
4. Under `Build and deployment`, choose:
   - Source: `Deploy from a branch`
   - Branch: `main`
   - Folder: `/root`
5. Save.
6. GitHub will give you an HTTPS link.
7. Open that link on your phone.
8. Go to `Phone Camera`.

HTTPS matters because phone browsers usually require a secure page before allowing live camera access.

## Option 2: Same Wi-Fi Local Link

From the project folder, run:

```bash
python3 -m http.server 8026 --bind 0.0.0.0
```

Find the Mac IP address:

```bash
ipconfig getifaddr en0
```

Then open this on the phone:

```text
http://YOUR_MAC_IP:8026/
```

Example:

```text
http://192.168.1.248:8026/
```

This is good for photo upload. Live camera may still be blocked on some phones because it is not HTTPS.

## Option 3: Secure Tunnel

Use a tunnel when you want a temporary HTTPS link from your Mac.

Examples:

- Cloudflare Tunnel
- ngrok
- Tailscale Funnel

The tunnel should point to the local server:

```text
http://localhost:8026
```

Then open the HTTPS tunnel URL on the phone.

## Invoice Studio

The app supports three billing entity slots:

- `Invoice 1 / Main Shop`
- `Invoice 2 / EV Builds`
- `Invoice 3 / Parts Sales`

Each slot can save its own:

- logo
- accent color
- invoice style
- shop info
- terms
- invoice number
- date settings

Saved drafts currently live in browser local storage.

## Phone Camera Workflow

1. Open the app on your phone.
2. Go to `Phone Camera`.
3. Take or upload a photo.
4. Use `Auto Label`.
5. Confirm or edit:
   - item name
   - category
   - count
   - who it belongs to
   - check-in date
   - location
   - notes
6. Save the entry.

Saved entries can be edited or deleted.

## Making Enrique Actually Smart

The current prototype uses simple browser-side suggestions. The real smart version needs a backend.

Recommended backend flow:

```text
phone photo or camera snapshot
-> backend upload endpoint
-> OpenAI vision model
-> structured JSON result
-> inventory/job/customer database
-> Enrique assistant response
```

Example vision result:

```json
{
  "item_name": "electric scooter",
  "category": "Scooter",
  "count": 3,
  "condition": "used, one appears missing a charger",
  "belongs_to_guess": "unassigned customer item",
  "location_guess": "intake area",
  "next_action": "confirm owner and accessories"
}
```

## Security Camera Support

Most security cameras use RTSP or ONVIF. Browsers usually cannot read those streams directly.

Recommended flow:

```text
RTSP / ONVIF camera
-> backend camera bridge
-> snapshot every N seconds or on demand
-> OpenAI vision analysis
-> Enrique inventory/job alert
```

Useful backend tools later:

- Python FastAPI or Node/Express
- FFmpeg for RTSP snapshots
- SQLite or Postgres for storage
- OpenAI vision for labeling/counting

## Current Limitations

- Browser local storage is not shared between devices.
- Static GitHub Pages cannot store shop records on a server.
- Real AI labeling requires an API backend.
- Security cameras require a backend bridge.
- Live phone camera works best from HTTPS.

## Current Shop Camera Notes

Configured camera:

```text
Device Name: solar port camera
App/Maker: FONDAIL
SN: ppsl55553f7150d4429b
Location: electrifygarage's home
Network: shop
Signal Strength: 94%
IP Address: 192.168.1.206
MAC Address: 98:a3:16:89:8d:60
Timezone: America/New_York
Device Platform: B411B
Device Version: 6.1.3.20250718
```

Local probe result from the Mac:

- Device responded on LAN at `192.168.1.206`.
- MAC matched `98:a3:16:89:8d:60`.
- HTTP `:80` refused.
- RTSP `:554` refused.
- RTSP alternate `:8554` refused.
- Common web/stream ports were refused or timed out.

Current conclusion:

This looks like a FONDAIL battery/solar app-first camera. It may not expose a normal local RTSP/ONVIF/browser stream unless the FONDAIL app has a setting for local/NVR/RTSP/ONVIF access.

To get it into Enrique, use one of these paths:

1. Enable RTSP/ONVIF/local streaming in the camera app if available, then paste that stream URL into `Camera Sources`.
2. Use the vendor app/cloud page if it provides a browser-viewable link.
3. Use a backend bridge if a stream URL becomes available.
4. Replace/add one always-on RTSP/ONVIF shop camera for Enrique vision.
