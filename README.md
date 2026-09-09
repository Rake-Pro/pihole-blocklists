# pihole-blocklists

Pi-hole adlists maintained for the rake.pro homelab. ABP-style entries (`||domain^`), so each line covers the domain and all subdomains. Works on Pi-hole v5.x and v6.

## Lists

| File | Scope | Raw URL |
|------|-------|---------|
| `lgtv.txt` | LG webOS TV: ACR, ads, telemetry, ThinQ cloud, log upload. Safe for all clients. | `https://raw.githubusercontent.com/Rake-Pro/pihole-blocklists/main/lgtv.txt` |
| `lgtv-tv-only.txt` | LG webOS TV: Hue discovery + built-in Netflix/Prime heartbeats. **TV group only.** | `https://raw.githubusercontent.com/Rake-Pro/pihole-blocklists/main/lgtv-tv-only.txt` |
| `samsung.txt` | Samsung Tizen TV: ACR, log collection, Samsung Ads, Prime/Netflix beacons. Safe for all clients. | `https://raw.githubusercontent.com/Rake-Pro/pihole-blocklists/main/samsung.txt` |
| `samsung-tv-only.txt` | Samsung Tizen TV: TV Plus, Smart Hub push, SmartThings, built-in Netflix/Prime/YouTube. **TV group only.** | `https://raw.githubusercontent.com/Rake-Pro/pihole-blocklists/main/samsung-tv-only.txt` |

## How to add (Pi-hole v6)

- Lists -> Add. Type must be **Block** (the default in the UI is Allow on some builds - check the toggle). An allow-type copy parses 0 domains and does nothing.
- `lgtv.txt`, `samsung.txt`: group `Default`.
- `*-tv-only.txt`:
  - Groups -> one group for smart TVs (e.g. `smart-tvs`).
  - Clients -> add each TV by MAC (covers IPv4 + IPv6), groups `Default` + `smart-tvs`. Keeping `Default` is required or gravity stops applying to the TV.
  - Lists -> Add -> paste URL -> group `smart-tvs` only.
- Tools -> Update Gravity. Lists show 0 domains until gravity has run once.
- Verify: Tools -> Search Adlists for `aic.cdpsvc.lgtvcommon.com` (LG) or `tvp-ads-log.samsungads.com` (Samsung) should name the list. Then watch the Query Log for the TV client: matching queries flip to `Blocked (gravity)` on its next poll (LG polls every 10-15 min).

## LG: what was left unblocked, and why

| Domain | Role | Reason kept |
|--------|------|-------------|
| `lgtvonline.lge.com`, `snu.lge.com`, `ngfts.lge.com`, `aic-gfts.lge.com`, `aic-ngfts.lge.com` | Firmware update check + download | Block only if you want to freeze firmware. |
| `us.lgtvsdp.com` | Service Delivery Platform: terms/consent sync, launcher config, region | Blocking causes network-error nags on some firmware. Try it after the rest is stable. |
| `www.ueiwsp.com` | UEI QuickSet: HDMI device identification, IR code DB for Magic Remote universal control | CEC itself is local, but auto-naming HDMI inputs / soundbar IR setup uses it. Try blocking if the remote only drives CEC devices. |
| `mediaservices.cdn-apple.com` | AirPlay 2 / HomeKit on the TV | Needed if AirPlay to the TV is used. |

## Samsung: what was left unblocked, and why

| Domain | Role | Reason kept |
|--------|------|-------------|
| `*.samsungqbe.com` (osb-*, scs, gpm) | Smart Hub "Online Service Broker": terms, auth, app catalog | Backbone of the Smart Hub UI; blocking yields error popups. Try after the rest is stable. |
| `time.samsungcloudsolution.com`, `cdn.samsungcloudsolution.com`, `oempprd.samsungcloudsolution.com`, `*.samsungcloudcdn.com` | Time sync, firmware/app CDN, module updates | Firmware path. |
| `api.sesupdate.com`, `gpms.sesupdate.com` | Samsung software/service update check-in | Update path, purpose only partly documented. |
| `www.ueiwsp.com`, `reverselookup.quicksetcloud.com` | UEI QuickSet: HDMI device identification, IR code DB | CEC is local, but HDMI auto-naming / universal-remote setup uses it. |
| `mediaservices.cdn-apple.com` | AirPlay 2 / HomeKit | Needed if AirPlay to the TV is used. |
| `pool.ntp.org` | Time | Leave. |

## Samsung TV-side settings

- Settings -> General -> Terms & Privacy (or Support -> Terms & Policies): **Viewing Information Services: Off** (this is ACR), **Interest-Based Advertising: Off**, **Voice Recognition Services: Off**.
- Settings -> General -> Smart Features: **Autorun Smart Hub: Off**, **Autorun Last App: Off**, **Samsung TV Plus: uninstall/disable** if the firmware allows.
- Settings -> General -> System Manager -> Ambient Mode / Art Mode auto-start: Off (it is the push feed consumer).
- Settings -> General -> Network -> Expert Settings: **Power On with Mobile: Off**, **IP Remote: Off** unless Home Assistant uses them.
- Do not sign in to a Samsung account; skip SmartThings setup.

## LG TV-side settings (DNS blocks stop uploads, these stop collection)

- Settings -> General -> System -> Additional Settings -> **Live Plus: Off** (this is ACR).
- Settings -> Support -> Privacy & Terms -> User Agreements: decline **Viewing Information**, **Voice Information**, **Interest-Based Advertisement**, **Live Plus**. Keep only the base Terms of Use.
- Settings -> Support -> Privacy & Terms -> Advertisement: **Limit AD Tracking: On**, then **Reset AD ID**.
- Settings -> General -> Additional Settings -> Home Settings: **Home Promotion: Off**, **Content Recommendation: Off**.
- Settings -> General -> AI Service: **AI Recommendation: Off**, voice recognition off.
- Do not sign in to an LG account / ThinQ; skip Home Hub setup.
- Optional hardening at the router: force the TV's UDP/TCP 53 to Pi-hole and drop 853 + known DoH endpoints, in case a firmware update adds a hard-coded resolver.
