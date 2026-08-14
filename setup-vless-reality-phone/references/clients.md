# Phone clients

## iPhone: Shadowrocket first

AWS → Ubuntu → VLESS + REALITY → iPhone Shadowrocket is the practically proven phone route in this skill group. The working sequence added a separate phone UUID to the existing server, corrected REALITY/TLS fields, left Proxy Pass empty, used Proxy routing with fallback off, and verified real phone traffic through the intended server. Preserve those invariants while adapting current UI labels.

Install only from Apple's App Store and confirm the seller is Shadow Launch Technology Limited:

- https://apps.apple.com/app/shadowrocket/id932747118

Explain that Shadowrocket is a paid networking client and supplies no server. Add a VLESS profile. Set raw/TCP transport, REALITY security, XTLS Vision flow, the configured SNI, Chrome fingerprint, client public-key/REALITY-password value, and short ID. Approve the iOS VPN configuration.

Use this mapping; labels vary by release:

```text
Type: VLESS
Address: SERVER_IP
Port: 443
UUID: PHONE_UUID authorized on server
Encryption: none/blank
Transport: none/raw/TCP
Flow or XTLS: xtls-rprx-vision
TLS/Security: Reality
SNI/Server Name: TARGET_DOMAIN
Fingerprint/uTLS: chrome
Public Key/Password: CLIENT_REALITY_PASSWORD
Short ID: SHORT_ID
ALPN: default/blank
Allow Insecure: off
Mux: off initially
Proxy Pass: empty
```

For initial validation, select this profile, set **Global Routing: Proxy**, disable fallback to unrelated nodes, and enable the main switch. `Config` means follow the selected ruleset and may send traffic direct; inspect its rules before calling it a bypass mode.

Prefer manual entry. If a QR/share link is used, keep it private and delete unnecessary copies.

If another VPN is active, warn before disconnecting it and record how to restore it. Enable Shadowrocket's VPN only for the test, validate Wi-Fi and cellular, then switch Shadowrocket off before reconnecting the previous VPN. A conflict between VPN profiles is not evidence that the server failed.

## Other iPhone clients second

If Shadowrocket is unavailable, use the regional App Store to find a reputable maintained client that explicitly lists VLESS + REALITY, XTLS Vision and uTLS/fingerprint support in its current version. Verify its developer, update recency, privacy information, and exact REALITY fields. Do not assume generic VLESS support includes REALITY. V2Box may be evaluated from its current official App Store listing, but do not claim it was personally proven here.

## Android third

Use the official v2rayNG project and its linked releases:

- https://github.com/2dust/v2rayNG

Use a current release because REALITY compatibility evolves. Add VLESS manually and enter the exact value sheet. Approve Android's VPN prompt. Do not use APK mirrors.

## Phone-specific failures

- Missing REALITY/Public Key field: update the client or choose a client explicitly supporting REALITY.
- Works on another device: compare app version and every profile field; preserve the server.
- Works only on Wi-Fi or cellular: diagnose network/port filtering before changing credentials.
- Connects but no proxied exit IP: ensure the profile, VPN switch, and routing mode are active.
- Previous VPN will not reconnect: first turn off the current proxy/VPN tunnel, then restore the previous profile; do not rotate VLESS credentials.
- Imported link with a manually changed UUID fails: add that UUID to the server `users` list, validate/restart, then re-import verified values.
- TLS/REALITY off: enable REALITY and fill SNI, Vision flow, public value and short ID; never use Allow Insecure as a repair.
- Stable exit not obtained: select the intended node, use Proxy routing for the test, leave fallback and Proxy Pass off, then recheck exit IP.

## Completion

Require a normal HTTPS page, exit IP equal to `SERVER_IP`, one Wi-Fi and one cellular test, and a clean disconnect that restores direct networking. Latency alone is insufficient.
