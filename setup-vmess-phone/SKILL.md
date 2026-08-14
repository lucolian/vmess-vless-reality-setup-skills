---
name: setup-vmess-phone
description: Set up, validate, and troubleshoot VMess for a telephone or mobile device. Use when the user's destination device is an iPhone or Android phone; prioritize iPhone with Shadowrocket, then other reputable iPhone clients, then Android with v2rayNG, while also providing a concise Ubuntu Xray server path when no VMess server exists.
---

# Set up VMess on a phone

Guide a beginner one checkpoint at a time. Start with the phone and existing-server question, not the cloud provider. At each checkpoint, say what success looks like.

- If yes, collect address, port, UUID, transport, TLS mode, and label; skip server creation.
- If no, read [references/aws-ec2-beginner.md](references/aws-ec2-beginner.md) for AWS and [references/server.md](references/server.md) for Xray/provider details before configuring the phone.

The owner's AWS → Ubuntu → VMess → Windows v2rayN route is practically proven, so it can support a phone-client setup without changing the server. No phone route is claimed as personally end-to-end proven. If the Windows client works, preserve the server and diagnose the phone only.

## Client priority

1. iPhone: Shadowrocket from Apple's App Store.
2. iPhone: another reputable VMess-capable App Store client when Shadowrocket is unavailable or the user declines a paid app.
3. Android: v2rayNG from its official project/release channel.
4. Android: another reputable, actively maintained VMess-capable client only if v2rayNG cannot be used.

Never describe Shadowrocket as an Android app. Do not provide shared Apple IDs, unofficial IPA files, cracked apps, mirror APKs, or sideloading workarounds.

## Configure iPhone with Shadowrocket first

Read [references/clients.md](references/clients.md). Prefer manual entry for a single server so the beginner understands each field. Use a QR/share link only when it was generated locally from already verified values, and warn that it contains the credential.

Require this exact mapping for the basic server:

```text
Type: VMess
Address: server public IPv4
Port: server VMess port
User ID/UUID: server UUID
Alter ID: 0 if shown
Encryption: auto
Transport: raw/tcp
TLS: off/none
```

Approve the iOS VPN configuration prompt, select the profile, connect, load a normal webpage, and compare an IP-check site's result with the server public IP. Then repeat once on cellular data so a Wi-Fi-only success is not mistaken for universal reachability.

For stable full-device egress during the test, choose the app's global/proxy routing mode and disable fallback to unrelated nodes. Explain that a rule/config mode may intentionally send some traffic directly. After validation, let the user choose global or rule-based routing knowingly.

## Configure alternatives

- For another iPhone client, map the same fields and leave host, path, SNI, certificates, and advanced transport settings empty/default because the server does not use them.
- For Android, use v2rayNG from https://github.com/2dust/v2rayNG and map the same fields; approve the Android VPN prompt and test Wi-Fi plus cellular.

## Guardrails and diagnosis

- Treat the UUID and share link as secrets; redact screenshots.
- Do not change the server while diagnosing a phone-only failure if another client works.
- Compare every field character for character before rotating credentials.
- Test both Wi-Fi and cellular. If one works, inspect that network rather than rebuilding Xray.
- A latency-test failure alone is inconclusive; require actual traffic and matching exit IP.
- iOS and Android generally permit only one active VPN/network-extension tunnel. Record the existing VPN state before switching and restore it afterward.
- When a new phone UUID is used, add that UUID to the server's VMess user list; changing only an import link cannot authorize a new credential.
- Read the installed Xray version, select its matching official schema, and interpret validation errors for the user. Do not make a beginner resolve current-versus-legacy field names.
- Troubleshoot in this order: phone VPN permission/profile; exact client fields; Wi-Fi versus cellular; server public IP; cloud/Ubuntu firewall; Xray listener/logs.

Report success only after real phone traffic exits from the server IP.
