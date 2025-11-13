# OBS-HTML-Scroller

An HTML/CSS/JS **Scroller overlay** for OBS Browser Source.  
Plays **text**, **images**, and **sounds** with per-item scroll direction, speed, fades, waits, Markdown, **live queue** inserts, optional **WebSocket** push, and **attached audio** (sound + visual in sync).

---

## Features
- JSON playlist (`data.json`) — items: `text | image | sound`
- 4-way scroll: `up | down | left | right` (or `1..4`; 1=down, 2=up, 3=right, 4=left)
- Per item: `speed` (px/s), `wait` (ms), `fadeIn`/`fadeOut` (ms), `dir`; set `speed: 0` to center & hold
- Fades with auto edge-mask, **Markdown** rendering (`?md=1` or `"md": true`)
- **Hotkeys**: Space = pause/resume, →/`N` = skip, `r` = reload (when `?debug=1`)
- **Live inserts** via `queue.json` (`queueMode=next|tail|immediate`) and optional **WebSocket** (`?ws=…`)
- **Attached audio** on text/image via `"audio": { … }`; and standalone `"type":"sound"` items
- **Theme presets** (`?theme=clean|glass|bold`) + URL CSS overrides (`?fs`, `?color`, …)
- Auto-reload main list (`?refresh=seconds`) and cache-buster (`&cb=TIMESTAMP`)

---

## Repo Layout

---

## Quick Start
1) Start a local server in the repo directory:
```bash
python3 -m http.server 8000

http://127.0.0.1:8000/scroller.html?src=data/data.json&dir=up&speed=120&fi=600&fo=600&wait=800&loop=1&md=1&debug=1&cb=1731080000000


Recommended Browser Source settings:
Width/Height = your canvas (e.g., 1920×1080)
“Refresh browser when scene becomes active” 
“Shutdown source when not visible”
If audio is routed by OBS: enable Control audio via OBS; otherwise leave it off to play to desktop.


JSON Examples
{
  "items": [
    { "text":"Welcome ","speed":0,"wait":1200,"fadeIn":400,"fadeOut":400 },
    { "type":"image","src":"assets/Tux.png","height":"42vh","dir":"left","speed":90,"wait":900,"fadeIn":300,"fadeOut":300 },
    { "text":"Left → Right marquee","dir":"right","speed":140,"wait":800 },
    { "type":"sound","src":"assets/DF_Alert-000.wav","volume":1,"duration":1800,"fadeIn":150,"fadeOut":200 },
    { "text":"New build is live!","dir":"left","speed":120,"fadeIn":400,"fadeOut":400,
      "audio": { "src":"assets/DF_Alert-000.wav","volume":1,"offset":0,"duration":0,"fadeIn":150,"fadeOut":200,"loop":false } }
  ]
}

queue.json (played next / immediate)
{ "items": [ { "text":" Hotfix deployed","speed":0,"wait":1500,"fadeIn":300,"fadeOut":300 } ] }


Live Inserts & Push
OBS URL with queue + faster polling
http://127.0.0.1:8000/scroller.html?src=data/data.json&queue=data/queue.json&queueMode=immediate&queueRefresh=0.5&md=1&dir=up&speed=120&fi=600&fo=600&wait=800&loop=1&theme=glass&cb=1731080000000


WebSocket push (optional)
Add ?ws=ws://127.0.0.1:5678 to the URL and send either:
{ "items": [ { "text":"Live insert via WS","speed":0,"wait":1200 } ] }


Themes & CSS Overrides
Presets: ?theme=clean | glass | bold (set theme=none to skip)
URL overrides (win over theme):
?fs=52 or 52px (font size), ?lh=1.3 (line height)
?color=%23FFEEAA (text), ?mw=90vw (max width), ?pad=2vh%204vw
?fade=1 (edge mask on/off), ?bga=0.05 (bg alpha), ?shadow=…, ?font=Inter,ui-sans-serif
Markdown default: ?md=1 (or per item "md": true)
Other: ?refresh=2 (poll main JSON every 2s), ?startDelay=500


Hotkeys
Space — pause / resume
→ / N — skip current item
r — reload (when ?debug=1)


The queue is a lightweight alert bus. Quick recipe:
OBS URL: &queue=data/queue.json&queueMode=immediate&queueRefresh=0.5 (instant inserts).
Payload shape (per alert): {"text": "...", "speed":0, "wait":1200, "fadeIn":200, "fadeOut":200, "audio":{"src":"assets/alert.wav"}}
– Use speed:0 for pop-up alerts; add type:"image" for badges/icons.
Priorities: push high-priority as single items; batch low-priority as { "items":[ ... ] }.
Debounce: your bot adds a short cooldown to avoid alert spam.
Uniqueness: include a tiny nonce ("t": 1731...) so file-change detection triggers every time.
Styling: swap looks via ?theme=glass (URL) or per-item md:true with emoji for fast branding.
Fail-safe: if WS is down, write to queue.json on disk—same payloads still work.
Bottom-right debug line appears with ?debug=1.




