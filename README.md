# pihole-blocklists

Pi-hole adlists maintained for the rake.pro homelab. ABP-style entries (`||domain^`), so each line covers the domain and all subdomains. Works on Pi-hole v5.x and v6.

## Lists

| File | Scope | Raw URL |
|------|-------|---------|
| `lgtv.txt` | LG webOS TV: ACR, ads, telemetry, ThinQ cloud, log upload. Safe for all clients. | `https://raw.githubusercontent.com/Rake-Pro/pihole-blocklists/main/lgtv.txt` |
| `lgtv-tv-only.txt` | LG webOS TV: Hue discovery + built-in Netflix/Prime heartbeats. **TV group only.** | `https://raw.githubusercontent.com/Rake-Pro/pihole-blocklists/main/lgtv-tv-only.txt` |

## How to add (Pi-hole v6)

- `lgtv.txt`: Lists -> Add -> paste URL -> group `Default`.
- `lgtv-tv-only.txt`:
  - Groups -> add group `LG TV`.
  - Clients -> add the TV by MAC (covers IPv4 + IPv6), groups `Default` + `LG TV`. Keeping `Default` is required or gravity stops applying to the TV.
  - Lists -> Add -> paste URL -> group `LG TV` only.
- Tools -> Update Gravity.

## What was left unblocked, and why

| Domain | Role | Reason kept |
|--------|------|-------------|
| `lgtvonline.lge.com`, `snu.lge.com`, `ngfts.lge.com`, `aic-gfts.lge.com`, `aic-ngfts.lge.com` | Firmware update check + download | Block only if you want to freeze firmware. |
| `us.lgtvsdp.com` | Service Delivery Platform: terms/consent sync, launcher config, region | Blocking causes network-error nags on some firmware. Try it after the rest is stable. |
| `www.ueiwsp.com` | UEI QuickSet: HDMI device identification, IR code DB for Magic Remote universal control | CEC itself is local, but auto-naming HDMI inputs / soundbar IR setup uses it. Try blocking if the remote only drives CEC devices. |
| `mediaservices.cdn-apple.com` | AirPlay 2 / HomeKit on the TV | Needed if AirPlay to the TV is used. |

## TV-side settings (DNS blocks stop uploads, these stop collection)

- Settings -> General -> System -> Additional Settings -> **Live Plus: Off** (this is ACR).
- Settings -> Support -> Privacy & Terms -> User Agreements: decline **Viewing Information**, **Voice Information**, **Interest-Based Advertisement**, **Live Plus**. Keep only the base Terms of Use.
- Settings -> Support -> Privacy & Terms -> Advertisement: **Limit AD Tracking: On**, then **Reset AD ID**.
- Settings -> General -> Additional Settings -> Home Settings: **Home Promotion: Off**, **Content Recommendation: Off**.
- Settings -> General -> AI Service: **AI Recommendation: Off**, voice recognition off.
- Do not sign in to an LG account / ThinQ; skip Home Hub setup.
- Optional hardening at the router: force the TV's UDP/TCP 53 to Pi-hole and drop 853 + known DoH endpoints, in case a firmware update adds a hard-coded resolver.
