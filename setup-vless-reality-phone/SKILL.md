---
name: setup-vless-reality-phone
description: Set up, validate, and troubleshoot VLESS with XTLS Vision and REALITY for a telephone or mobile device. Use when the destination device is an iPhone or Android phone; prioritize iPhone with Shadowrocket, then other reputable iPhone clients, then Android with v2rayNG, while also providing a concise Ubuntu Xray server path when no VLESS + REALITY server exists.
---

# Set up VLESS + REALITY on a phone

Guide a beginner one checkpoint at a time. Start with the phone and existing-server question. At each checkpoint, state what success looks like. Ask whether a current working VLESS + REALITY server or verified link exists.

- If yes, collect all client values below and skip server creation.
- If no, read [references/aws-ec2-beginner.md](references/aws-ec2-beginner.md) for AWS and [references/server.md](references/server.md) for Xray/provider details; create the server first.

The owner's AWS Windows and AWS macOS computer routes are practically proven. AWS → Ubuntu → VLESS + REALITY → iPhone Shadowrocket is also proven after adding a separate phone UUID to the already-working server, correcting REALITY/TLS fields, leaving Proxy Pass empty, using Proxy routing with fallback off, and verifying real phone traffic through the intended server. Do not extend this claim to Android, other iPhone clients, Azure, another VPS, or a different protocol. If a proven computer client works, preserve the server and diagnose phone fields, app version, routing mode, and network first.

## Client priority

1. iPhone: Shadowrocket from Apple's App Store.
2. iPhone: another reputable, current App Store client explicitly supporting VLESS + REALITY.
3. Android: current v2rayNG from its official project.
4. Android: another current reputable client only when v2rayNG cannot be used.

Never describe Shadowrocket as an Android app. Do not provide shared Apple IDs, unofficial IPA files, cracked apps, mirror APKs, or sideloading workarounds.

## Required client values

```text
Address = server public IPv4
Port = 443 or configured port
Protocol = VLESS
UUID = server user ID
Flow = xtls-rprx-vision
Encryption = none
Transport = raw/tcp
Security = reality
SNI/Server name = configured REALITY serverName
Fingerprint = chrome
Public key/Reality password = public value derived from server private key
Short ID = configured shortId
SpiderX = / or client default
```

Current official Xray client JSON names the public-key value `password`; Shadowrocket and other GUIs may label it `Public Key`. It is not the server private key. Never copy the private key to a phone.

## Configure iPhone with Shadowrocket first

Read [references/clients.md](references/clients.md). Add a VLESS profile manually and fill every value above. Prefer manual entry for learning and diagnosis. Use a QR/share link only when generated locally from verified values; warn that it contains secrets.

Approve the iOS VPN configuration, select the profile, connect, load a normal webpage, and compare the exit IP with the server IP. Repeat on cellular data after Wi-Fi succeeds.

For a stable-exit test in Shadowrocket, select the intended server, choose global routing **Proxy**, and leave fallback off. `Config` is rule-based and may send some traffic directly; it is not automatically equivalent to a mainland-China bypass rule. After the end-to-end test, explain the privacy/cost/latency tradeoff and let the user choose Proxy or a verified ruleset.

## Configure alternatives

- For another iPhone app, first verify its current release explicitly supports REALITY and XTLS Vision. Map the same fields; do not disable certificate verification.
- For Android, use current v2rayNG from https://github.com/2dust/v2rayNG, enter the same fields, approve the Android VPN prompt, and test Wi-Fi plus cellular.

## Guardrails and diagnosis

- Treat the UUID, client REALITY password/public key, short ID, and share link as secrets; redact screenshots.
- Confirm before turning off another phone VPN. iOS and Android generally allow only one active VPN tunnel; preserve the old VPN details so the user can restore it after testing.
- If another device works, preserve the server and diagnose the phone profile/version/network first.
- Compare every field exactly, especially flow, SNI, fingerprint, public-key/password, and short ID.
- A latency-only failure is inconclusive; require real traffic and matching exit IP.
- If Wi-Fi and cellular differ, investigate that network before rebuilding Xray.
- Use current clients; do not lower server minimum-client behavior merely to support an obsolete app without explaining the tradeoff.
- Own version compatibility and target interpretation. Read the installed Xray version, choose its matching official schema, interpret validation errors, and evaluate `xray tls ping` output. Do not delegate those technical judgments to the beginner.
- A new phone UUID must also be present in the server `users` list. Never create a link by replacing only the UUID unless the server was updated and validated first.
- Keep Proxy Pass/chaining empty for the direct AWS profile unless the user intentionally wants and understands a chained proxy.
- When creating AWS resources, retain the MFA, non-root daily administration, billing-alert, cost-warning, and full-cleanup safeguards in [references/server.md](references/server.md).

Report success only after real phone traffic exits through the server.
