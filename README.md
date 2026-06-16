# LE HSCR — SIGINT Radio Monitor

**One local dashboard for Bluetooth LE, Wi‑Fi, and satellite TV — live scans, spectrum, capture, and playback on hardware you control.**

SIGINT Radio Monitor (LE HSCR) is a **dense RF lab console** in the browser. Plug in USB Bluetooth and Wi‑Fi adapters (and optionally a DVB‑S tuner or Sat>IP box), open the UI, and work across three radio domains without juggling a dozen terminal tools. BLE devices appear on a radar with iBeacon intelligence; Wi‑Fi networks show spectrum waterfalls and probe traffic; satellite transponders scan, lock, and play FTA channels with live signal meters.

Built for **authorized research** on networks and airspace you own or may legally test — home lab, bench, or field kit. Runs **locally** (Docker Kali recommended); not cloud SaaS.

**Made by [Logic Encoder](https://logicencoder.com)**

Private source: [logicencoder/le_hscr](https://github.com/logicencoder/le_hscr)

---

## What you can do

| Domain | In plain language |
|--------|-------------------|
| **Bluetooth LE** | See every advertiser in range, track a device, watch a beacon UUID, replay session history, export JSON/CSV |
| **Wi‑Fi** | Scan networks, view spectrum and channel occupancy, sniff probe requests, list clients, capture handshakes, run vuln checks on an AP |
| **Satellite (DVB‑S)** | Scan transponders on a dish, browse TV/radio services, watch FTA in the browser, analyze MPEG transport streams |
| **USB** | See all radios on the bus, recover stuck USB, reset every module in one action |
| **Logs** | Cross-radio KPIs, hardware detection, unified event stream |

Everything updates in **real time** over WebSocket — device lists, spectrum traces, capture status, SAT lock meters, and alerts without refreshing the page.

---

## Example use cases

### 1. Track BLE beacons in a venue

You are mapping which iBeacons are active in a building you operate (your store, your event space).

1. Start the stack with `./start-kali.sh` and open the UI.
2. Open **BT**, turn **Scan ON** (active mode for faster discovery).
3. Watch the **radar** and device list — iBeacon and Eddystone badges appear on rows automatically.
4. Click a device → open detail → **Watch beacon** on its UUID.
5. When that UUID is seen again, you get a **toast alert** (`ble_alert`) even if the random MAC rotated.
6. Export the session as **JSON** or **replay** the last hour from DuckDB to see RSSI movement on the **heatmap** and 60‑minute chart.

**What you get:** proof of which beacons are live, when they appeared, and how signal strength changed — without a separate sniffer app.

### 2. Wi‑Fi lab on your own access point

You own a test AP and want to characterize spectrum use and capture a WPA handshake for crack testing in your lab.

1. Attach **two Wi‑Fi dongles** — in **Settings**, assign one to **scan/connect** and one to **monitor**.
2. **Wi‑Fi → Scan** — rescan, open **spectrum** (trace + waterfall + channel occupancy) on 2.4 and 5 GHz.
3. **Wi‑Fi → Probes** — enable monitor mode; see which SSIDs nearby devices are probing for.
4. **Wi‑Fi → Capture** — start EAPOL capture on the monitor radio, or run **Handshake lab** with your AP’s BSSID for 30 seconds.
5. Download **pcap** or **hc22000** from capture history when EAPOL frames appear.
6. On **Lab**, run **DuckDB analytics** (channel distribution) or **deauth check** only on the SSID you control.

**What you get:** one screen for scan → observe → capture → export — instead of memorizing `airodump-ng` / `tcpdump` flags for each step.

### Bonus — satellite FTA watch (when DVB hardware is connected)

1. **DVB → Scan** — enable DVB power, pick satellite and LNB/DiSEqC, run transponder scan.
2. Browse **TV/Radio** services; click a transponder to **tune**.
3. **Watch** — play the channel in-browser (MPEG‑TS low latency or HLS); live **Sig/SNR/quality** meters in the header.
4. **TS Analyzer** — PID table and SI tree on the locked transponder.

---

## Dashboard — five main areas

### BT (Bluetooth LE) — default tab

**KPI strip:** pinned devices, recent sightings (120 s), updates per minute, average RSSI, strongest signal, session event count.

**BLE Control:** scan on/off, **active vs passive** mode, scan now, **Ping area** (12 s RSSI burst), Bluetooth power on/off, **RF reset** (adapter cycle).

**Visualization:**
- **Radar** — classic or pulse style; devices by bearing/RSSI
- **Adv traffic** — 60 s waterfall (advertisements per second), channels **37 / 38 / 39**, manufacturer chart, btmon status
- **Heatmap** — 60 s RSSI history; filterable event log (All / New / RSSI / Sys)

**Device list:** search, **Tracked** filter, clear session, export **JSON** or **CSV** from header.

**Device detail drawer:** track device, watch iBeacon UUID, 60‑min RSSI chart, advertisement intelligence (services, manufacturer, proximity estimate, vendor from OUI).

**BT Tools (optional):** start/stop **Bettercap** passive BLE recon, **Bluelog** export/import, **replay** DuckDB timeline at chosen speed.

**Alerts:** server-side rules → WebSocket alert + UI toast/beep when conditions match.

---

### Wi‑Fi — six subtabs

| Subtab | What you do |
|--------|-------------|
| **Scan** | Managed or monitor mode; rescan; RF reset; network list with security/channel/RSSI; click row → detail + **vuln scan**; spectrum **scan/survey**; trace MHz pan/zoom; waterfall; channel occupancy; 2.4 + 5 GHz rows; heatmap |
| **Probes** | Monitor mode; devices sending probe requests; top SSIDs chart; export CSV |
| **Clients** | Start/stop **airodump-ng** feed; client ↔ AP pairs |
| **Capture** | EAPOL capture (tcpdump or hcxdumptool); **handshake lab** (BSSID + optional client, 30 s); download pcap / hc22000 |
| **Lab** | Deauth burst (with injection check); DuckDB Wi‑Fi/BLE analytics; **cross-radio** vendor correlation |
| **Adapter** | List adapters; monitor on/off; connect/disconnect SSID; **hotspot** start/stop |

**Wi‑Fi detail drawer:** network fingerprint, **vuln scan** (nmap + searchsploit; optional arp-scan, wash, nikto, routersploit light).

**Dual-radio setup:** one adapter for everyday scan/connect, second dedicated to monitor — configured in **Settings** so you are not forced to flip one dongle between modes.

---

### DVB (Satellite) — four subtabs

| Subtab | What you do |
|--------|-------------|
| **Scan** | DVB power toggle; pick satellite, LNB, DiSEqC, dwell; full scan, single transponder, NIT scan, service scan; driver setup; card reset; transponder table (signal/SNR/quality/lock) |
| **Watch** | TV/Radio/Favorites lists; play/stop in browser; **MPEG‑TS** or **HLS**; deinterlace; live meters; stream log |
| **TS Analyzer** | SI tree, PID table, live analysis on tuned transponder |
| **Logs** | Scan log; clear/refresh |

**Hardware paths:** local USB DVB‑S (e.g. TechniSat, TBS) **or** **Sat>IP** remote tuner on LAN.

**Power gating:** DVB stays off until you enable it — scans and playback blocked until power on (protects hardware and USB budget).

---

### USB

- KPIs: devices, hubs, buses
- Full device table with rescan
- **Emergency recover** — host USB reset when adapters disappear
- **Reset ALL** — USB recover + clear SAT/BLE/Wi‑Fi session state
- Live attach/detach log

---

### Logs (cross-radio overview)

- Live summary cards: BLE, Wi‑Fi, monitor, DVB
- **Hardware** panel: auto-detected BT, Wi‑Fi, SAT, USB adapters
- Session uptime, event counts, feed health
- Per-module stats (BLE scan, Wi‑Fi scan, capture, BT tools)
- **All Events** stream with filters (All / New / RSSI / Sys / Err)

---

## Global controls (all tabs)

**Settings drawer:**
- Notifications, heatmap colors, font/layout
- Multi-radio Wi‑Fi role assignment (scan vs monitor)
- Spectrum layer toggles
- SAT stream mode (MPEG‑TS vs HLS), transcode vs copy, scan intervals
- **UI prefs sync** to server — layout and beacon watchlist follow you across browsers on the LAN

**Header:** connection indicator, context CSV export, clock.

---

## Real-time behaviour

On connect, WebSocket pushes full snapshots (BLE devices, Wi‑Fi networks, adapters, probe/client history, capture state, logs).

Live updates include:
- New/changed BLE devices and stats ticks
- Wi‑Fi networks, spectrum frames, probe and client rows
- Capture and airodump progress
- SAT scan progress, lock meters, TV play state
- USB topology changes
- Unified log lines and **BLE alerts**

SAT **Watch** also streams **MPEG‑TS** over HTTP or serves **HLS** segments for in-browser playback.

---

## BLE capabilities (detail)

- Continuous scan via Bleak; start/stop; active/passive; auto-start on boot (configurable)
- Adapter power on/off; **Ready** (power + active scan); hardware RF reset
- Vendor lookup (OUI), RSSI, stale/tracked pinning
- **iBeacon**, **Eddystone** (UID/URL/TLM), manufacturer data, service UUIDs, TX power, adv rate
- Optional **GATT fingerprint** per device (server flag — off by default)
- Optional **Ubertooth** capture hook (status when hardware absent)
- **btmon** integration for HCI-level adv traffic
- Session export JSON/CSV; DuckDB replay; cross-radio analytics with Wi‑Fi vendors

---

## Wi‑Fi capabilities (detail)

- **nmcli** managed scan; live network table
- Monitor mode for probes, clients, capture, deauth lab
- Spectrum trace + waterfall + per-channel occupancy (2.4 / 5 GHz)
- Time×network RSSI heatmap
- EAPOL / WPA handshake capture; handshake lab with optional deauth
- Connect/disconnect; software hotspot
- AP vuln scan pipeline (nmap, searchsploit, optional extras)
- DuckDB: channel histograms, SSID tops, timelines
- CSV export: networks, probes, clients

---

## Satellite capabilities (detail)

- Satellite/transponder **catalog** (Lyngsat-derived)
- Auto satellite identify (30°W–90°E range)
- Manual tune; single-TP scan; NIT; service/channel scan
- TV / Radio / NIT service lists; favorites; search/sort
- Browser playback with Sig/SNR/quality/lock/BER
- TS analyzer: CA systems, peers on transponder
- Optional **NVENC** GPU transcode (compose overlay)
- Session restore on restart

---

## How you run it

**Recommended:** Kali Linux in Docker — pentest tools (`iw`, `nmcli`, `tcpdump`, `aircrack-ng`, `bettercap`, …) inside the container; host needs **Docker + USB radios + network**.

```bash
./start-kali.sh
# UI → http://127.0.0.1:8765
```

**Alternative:** native Linux host with tools installed (`SIGINT_FORCE_HOST=1 ./start.sh`).

---

## Hardware you need

| Radio | Typical setup |
|-------|----------------|
| **Bluetooth** | USB dongle or built-in → appears as `hci0` |
| **Wi‑Fi** | USB adapter with **monitor mode**; **two dongles** ideal (scan + monitor at once) |
| **Satellite (optional)** | DVB‑S USB tuner **or** Sat>IP server on LAN with dish/LNB/DiSEqC |
| **Optional** | Ubertooth (advanced BLE capture); NVIDIA GPU for SAT transcode |

Single Wi‑Fi dongle can usually do **scan or monitor**, not both simultaneously — plan for two radios for full Wi‑Fi lab workflow.

---

## What it does not do

- **Not** a cloud or multi-tenant service — local machine only; no built-in telemetry
- **Not** for unauthorized surveillance — deauth, capture, and vuln tools are for **your** lab networks
- **Not** zero-hardware — real USB adapters required; SAT needs tuner or Sat>IP
- **Not** a mobile app — browser UI optimized for desktop/laptop lab bench
- **Not** guaranteed full Ubertooth UI without the dongle — hook is optional

Captures, session database, and `.env` stay on your machine — not published in the overview repo.

---

## Tech stack

| Layer | Technologies |
|-------|----------------|
| API | Python 3, FastAPI, Uvicorn, Pydantic, ORJSON |
| UI | Single-page HTML/JS — canvas/SVG viz; HLS.js + mpegts.js for SAT |
| Real-time | WebSocket wire contract (pytest-validated) |
| BLE | Bleak, BlueZ, btmon; optional Bettercap, Bluelog |
| Wi‑Fi | NetworkManager/nmcli, iw; tcpdump, hcxdumptool, airodump-ng, aircrack-ng |
| Recon | nmap, searchsploit, nikto, arp-scan, wash, routersploit (light) |
| SAT | dvb-tools, ffmpeg; Sat>IP client; optional NVENC |
| Storage | DuckDB session DB; pcap and tooling logs on disk |
| Runtime | Docker (Kali rolling image) or native Linux |
| Quality | pytest, Playwright E2E, CI smoke on push |

---

## Related repositories

| Repository | Role |
|------------|------|
| [le_hscr](https://github.com/logicencoder/le_hscr) | Private application code |
| [le-hscr-overview](https://github.com/logicencoder/le-hscr-overview) | This product overview |

See [REPOS.md](REPOS.md).

---

**Made by [Logic Encoder](https://logicencoder.com)** · [GitHub](https://github.com/logicencoder) · [Contact](https://logicencoder.com/contact/)
