

# luci-app-3ginfo-lite

![GitHub release (latest by date)](https://img.shields.io/github/v/release/4IceG/luci-app-3ginfo-lite?style=flat-square)
![GitHub stars](https://img.shields.io/github/stars/4IceG/luci-app-3ginfo-lite?style=flat-square)
![GitHub forks](https://img.shields.io/github/forks/4IceG/luci-app-3ginfo-lite?style=flat-square)
![GitHub All Releases](https://img.shields.io/github/downloads/4IceG/luci-app-3ginfo-lite/total)

> \[!NOTE]
> Luci-app-3ginfo-lite is a fork from [obsy's easyconfig modem scripts](https://github.com/obsy/packages/tree/master/easyconfig/files/usr/share/easyconfig/modem)

## Improvements

This fork includes additional fixes and regional optimizations:

* **Fixed LTE-only SIM "Not Registered" issue** — prioritized accurate parsing of `+CEREG` over `+CREG` for Quectel modems
* **Network optimization for Sri Lanka** — enhanced support for local operators and LTE/CA reporting

## Supported Devices (partial list)

```bash
- ASKEY WWHC050
- BroadMobi BM806U (DLINK DWR-921 C1)
- DW5809e Dell Wireless 5809e Gobi 4G LTE Mobile Broadband Card (EM7305)
- DW5811e Snapdragon X7 LTE (EM7455B)
- DW5821e Snapdragon X20 LTE
- Fibocom FM150-AE
- Fibocom FM350-GL
- Fibocom L860-GL
- Huawei E3272/E3372/E3276
- Mikrotik R11e-LTE
- Mikrotik R11e-LTE6
- NL952-EAU in ECM mode (LTE CAT18)
- Qualcomm CDMA Technologies MSM
- Quectel EC20-E / EC200T / EC25 / EG06 / EG18-EA / EM12-G / EM160R-GL / EP06
- Quectel RG500Q-EA / RG502Q-EA / RM500Q-GL / RM500U-CNV / RM520N-GL
- Sierra Wireless 320u / EM7455 / EM9190 / MC7710
- SIMCOM SIM8200EA-M2
- Telit LE910-EUG / LN940 (QMI/MBIM) / LN940-CP
- YUGA CLM920-NC5
- ZTE MF821 / MF286 series / MF289F / MF28D / MF290
```

### Known Limitations

Some devices may require further testing or script adjustments:

* Fibocom FM150-AE
* Sierra Wireless EM9190 / MC7710
* SIMCOM SIM8200EA-M2
* ASKEY WWHC050
* Mikrotik R11e-LTE
* HiLink-based modems (ZTE / Alcatel)

## UI Preview

![luci-app-3ginfo-lite](https://github.com/4IceG/Personal_data/blob/master/zrzuty/luci-app-3ginfo-litemod.png?raw=true)

---

Let me know if you’d like to add contributor credits, commit links, or Sri Lanka operator-specific examples.
