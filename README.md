# 3ginfo-lite

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/nooblk-98/luci-app-3ginfo-lite?style=flat-square)](https://github.com/nooblk-98/luci-app-3ginfo-lite/releases)
[![GitHub stars](https://img.shields.io/github/stars/nooblk-98/luci-app-3ginfo-lite?style=flat-square)](https://github.com/nooblk-98/luci-app-3ginfo-lite/stargazers)
[![GitHub downloads](https://img.shields.io/github/downloads/nooblk-98/luci-app-3ginfo-lite/total?style=flat-square)](https://github.com/nooblk-98/luci-app-3ginfo-lite/releases)
[![License](https://img.shields.io/badge/License-GPLv3-blue.svg?style=flat-square)](LICENSE)

> **LuCI** application for monitoring 3G/4G/5G mobile broadband connections on OpenWrt routers. Displays real-time signal quality, network parameters, operator info, and cell details for mPCI-E, M.2, and USB modems.

Forked from [modemdata](https://github.com/obsy/modemdata) by Cezary Jackiewicz.

---

## Features

- **Real-time Modem Dashboard** — Signal strength (CSQ/RSSI/RSRP/RSRQ/SINR) with color-coded progress bars, operator name, MCC/MNC, Cell ID, LAC, TAC, connection time, and data usage (RX/TX).
- **Carrier Aggregation (CA)** — Displays primary (PCC) and up to 4 secondary (SCC) component carriers with band, PCI, and EARFCN.
- **5G Support** — Detects 5G SA (Standalone) and 5G NSA (Non-Standalone) modes with dedicated band mappings.
- **Multi-Modem Support** — Automatically detects and manages multiple modems via the `modemdefine` configuration system.
- **HiLink Modems** — Supports HiLink-mode Huawei, Alcatel, and ZTE modems via HTTP API (no AT port required).
- **SIM Card Details** — Displays IMSI, ICCID, IMEI, and SIM slot in an interactive modal.
- **BTS Search** — Look up base station locations by Cell ID using btsearch.pl or lteitaly.it.
- **Diagnostics Page** — Run USB device listing, tty port scan, and debug script execution for troubleshooting.
- **Live Polling** — Auto-refresh every few seconds with smooth progress bar animations.
- **Translations** — German, Italian, Polish, Russian, Chinese (Simplified).
- **Extensive Modem Database** — Vendor/product-specific scripts for dozens of Quectel, Huawei, ZTE, Fibocom, Sierra Wireless, Telit, and other modem models.

---

## Requirements

- **OpenWrt** 21.02+ (or LEDE)
- **LuCI** (JS-based interface)
- **Dependencies** (automatically installed with the package):
  - `sms-tool` — Modern AT command tool
  - `comgt` — Gcom scripting engine for legacy modems
  - `kmod-usb-serial-option` — USB serial driver for option-based modems

---

## Installation

### Using the IPK/APK package

Download the latest release from the [releases page](https://github.com/4IceG/luci-app-3ginfo-lite/releases) and install:

```bash
# For IPK-based systems (OpenWrt)
opkg install luci-app-3ginfo-lite*.ipk

# For APK-based systems (OpenWrt main)
apk add luci-app-3ginfo-lite*.apk
```

### Building from source

Use the OpenWrt build system:

```bash
# Clone feeds if not already done
./scripts/feeds update packages luci
./scripts/feeds install -a -p luci

# Copy the package into your custom package feed, then
make package/luci-app-3ginfo-lite/compile -j$(nproc)
```

---

## Configuration

After installation, navigate to **LuCI → Modem → Configuration** and set:

1. **Network Interface** — Select the WAN interface connected to your modem (default: `wan`)
2. **Device / IP Address** — Choose the modem port (e.g., `/dev/ttyUSB2`) or enter a HiLink modem IP (e.g., `192.168.8.1`)
3. **BTS Search Engine** — Optional: select a website (btsearch.pl or lteitaly.it) for Cell ID lookup

> [!TIP]
> The package auto-detects available serial ports in the system. If your modem appears as a network device (HiLink mode), enter its IP address directly.

---

## Usage

Once configured, open **LuCI → Modem → Details** to see the live dashboard.

### Main Dashboard (`3gdetail.js`)

| Section | Information displayed |
|---|---|
| **General Information** | Signal strength %, Operator name & location, SIM status, Connection time, Data RX/TX, Network technology |
| **Modem Information** | Model, Firmware revision, Communication port, Protocol (qmi/mbim/ecm/ncm), Chip temperature |
| **Cell / Signal Information** | MCC/MNC, Cell ID (dec/hex), TAC (dec/hex), LAC (dec/hex), CSQ, RSSI, RSRP, SINR, RSRQ — all with progress bars |
| **Carrier Aggregation** | Primary band (PCC) and up to 4 secondary bands (SCC1–SCC4) with PCI & EARFCN |

### Signal Quality Bars

Each signal metric is rendered as a color-coded progress bar:

| Color | CSQ | RSSI | RSRP | SINR | RSRQ |
|---|---|---|---|---|---|
| 🟢 Lime | Very good (>19) | Very good (>-70) | Very good (≥-80) | Excellent (>20) | Excellent (≥-10) |
| 🟡 Yellow | Good (14–19) | Good (-85 to -70) | Good (-90 to -79) | Good (13–20) | Good (-15 to -9) |
| 🟠 Dark Orange | Weak (10–13) | Weak (-100 to -86) | Weak (-100 to -89) | Mid cell (1–12) | Mid cell (-20 to -14) |
| 🔴 Red | Very weak (≤9) | Very weak (<-100) | Very weak (<-100) | Cell edge (≤0) | Cell edge (<-20) |

### Diagnostics (`3gdebug.js`)

Use the **Diagnostics** page to troubleshoot modem issues:

- **USB devices** — Show all connected USB devices with `cat /sys/kernel/debug/usb/devices`
- **tty ports** — List available serial ports with `ls /dev`
- **Debug output** — Run `sh -x /usr/share/3ginfo-lite/3ginfo.sh` to trace script execution

Results can be downloaded as a text file for sharing on forums.

---

## Architecture

```
LuCI Web UI (JavaScript)
    │
    ▼
/usr/share/3ginfo-lite/3ginfo.sh    ← Main data collection script (JSON output)
    │
    ├── detect.sh                    ← Auto-detect modem device
    ├── check.gcom                   ← Verify modem availability (gcom)
    ├── info.gcom                    ← Legacy AT command queries (gcom)
    ├── vendorproduct.gcom           ← Vendor/product identification
    ├── mccmnc.dat                   ← MCC/MNC → operator name mapping
    │
    └── modem/
        ├── usb/<vid>pid>/           ← Per-modem scripts (AT command parsing)
        ├── pci/<vid>pid>/           ← PCIe modem scripts
        └── hilink/                  ← HiLink modem support
            ├── huawei_hilink.sh
            ├── alcatel_hilink.sh
            └── zte.sh
```

The main script outputs a JSON payload which the LuCI frontend consumes and renders as the live dashboard. Signal values are polled every few seconds via `poll.add()` for real-time updates.

---

## Supported Modems

The package includes vendor/product-specific scripts for modems from:

| Vendor | Examples |
|---|---|
| **Quectel** | EC20, EC25, EG91, EG95, EM12, EM20, RM500, RM520, RM521, BG96, BG600 |
| **Huawei** | HiLink (E3372, E5577, etc.), ME909, MU609 |
| **ZTE** | MF289F, MF920, HiLink modems |
| **Fibocom** | NL668, NL678, NL952, L810, L860, L850 |
| **Sierra Wireless** | MC74xx, MC77xx, EM74xx |
| **Telit** | LN920, LE910 |
| **Alcatel** | HiLink modems |
| **SIMCom** | SIM7600 |
| **Others** | Generic AT-compatible modems also work with basic information |

> [!NOTE]
> For modems without a dedicated script, the package falls back to standard AT commands and will still report CSQ, operator, and basic registration info.

---

## Translations

| Language | File |
|---|---|
| German | `po/de/3ginfo.po` |
| Italian | `po/it/3ginfo.po` |
| Polish | `po/pl/3ginfo.po` |
| Russian | `po/ru/3ginfo.po` |
| Chinese (Simplified) | `po/zh_Hans/3ginfo.po` |

To add a new translation, copy `po/template/3ginfo.po` to `po/<code>/3ginfo.po` and translate the `msgstr` fields.

---

## Development

### Project structure

```
luci-app-3ginfo-lite/
├── Makefile                                  ← OpenWrt package metadata
├── htdocs/luci-static/resources/view/modem/  ← LuCI JavaScript views
│   ├── 3gdetail.js                           ← Main dashboard
│   ├── 3gconfig.js                           ← Configuration form
│   └── 3gdebug.js                            ← Diagnostics page
├── po/                                       ← Translations
├── root/
│   ├── etc/config/3ginfo                     ← Default UCI config
│   ├── etc/uci-defaults/                     ← Post-install setup
│   └── usr/share/3ginfo-lite/                ← Core scripts and modem data
└── .github/workflows/                        ← CI/CD for IPK/APK builds
```

### Building

```bash
# In an OpenWrt SDK environment
make package/luci-app-3ginfo-lite/clean
make package/luci-app-3ginfo-lite/compile -j$(nproc)
```

---

## Troubleshooting

1. **"Device not found"** — Check that the modem is detected in the system: `ls /dev/ttyUSB* /dev/ttyACM* /dev/wwan*`. Ensure `kmod-usb-serial-option` and relevant modem kernel modules are loaded.

2. **No signal data** — Verify the correct port is selected in Configuration. For multi-port modems, try different ttyUSB ports.

3. **HiLink modem not responding** — Make sure the modem is accessible at the configured IP address. Check if `wget` is installed on the router.

4. **SIM issues** — Use the Diagnostics page → USB devices to confirm the modem enumerates correctly. Check physical SIM card and PIN requirements.

5. **"Problem with registering to the network"** notification — Check antenna, SIM card registration, and APN configuration in the WAN interface.

---

## Acknowledgements

- [Cezary Jackiewicz (obsy)](https://github.com/obsy) — Original `modemdata` project
- [eko.one.pl forum](https://eko.one.pl/?p=openwrt-3ginfo) — Community discussion and support
- All contributors and testers from the OpenWrt community

---

## License

**GNU General Public License v3.0** — See [LICENSE](./luci-app-3ginfo-lite/LICENSE) for details.

Copyright 2021–2025 Rafał Wabik (IceG)
