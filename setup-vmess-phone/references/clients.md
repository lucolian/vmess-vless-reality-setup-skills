# Phone clients

## iPhone: Shadowrocket first

Use the Apple App Store listing:

- https://apps.apple.com/app/shadowrocket/id932747118

Confirm the seller is Shadow Launch Technology Limited. Explain that Shadowrocket is a paid client and provides no server service. In Shadowrocket, add a VMess profile with the server values, save it, select it, and enable the main connection switch. Approve the iOS VPN configuration when prompted.

Map the basic server exactly:

```text
Type: VMess
Address: SERVER_IP
Port: 16823
UUID/User ID: PHONE_UUID
Alter ID: 0 if shown
Encryption: auto
Transport: none/raw/tcp
TLS: off
Host/path/SNI: empty
Proxy Pass: empty
```

For the initial stable-exit test, choose **Global Routing: Proxy** and leave fallback off. A `Config` mode follows rules and may send some traffic directly; it is not proof of full-device proxying. Do not chain the profile through another commercial node unless that is an intentional design.

Prefer manual entry. If using a QR code, keep it off public screens and delete unnecessary copies because the QR contains the credential.

## Other iPhone clients second

If Shadowrocket is unavailable, search the user's regional App Store for a current reputable client that explicitly supports VMess. Verify the listing, developer, recent maintenance, privacy information, and VMess support at the time of use. Do not invent a universal recommendation because regional availability changes.

Map only address, port, UUID, auto encryption, raw/TCP transport, no TLS, and Alter ID 0 if visible.

## Android third

Use the official v2rayNG project and release links it publishes:

- https://github.com/2dust/v2rayNG

Add a VMess profile manually, approve the Android VPN permission, connect, and validate a webpage plus exit IP. Do not use APK repost sites.

Use the same field mapping. Start with global routing for the acceptance test, then choose rule-based routing only after explaining which traffic will bypass the server. Test Wi-Fi and cellular separately.

## Phone-specific failures

- VPN permission rejected: reopen the connection and approve the OS prompt.
- Works on Wi-Fi only: test whether the carrier blocks the port; do not alter credentials first.
- Works on cellular only: inspect Wi-Fi DNS/firewall/captive portal.
- Connects but local IP remains: ensure the desired profile and VPN switch are active.
- Battery/background disconnects: check OS low-power and background restrictions after core connectivity works.
- Existing computer works but phone fails: do not edit the server first; compare the phone UUID authorization, app version, fields, routing mode, and network.
- Imported profile points to old values: open it and compare fields; an imported link is not authoritative.

## Phone completion check

1. Intended profile is selected and the OS VPN indicator appears.
2. A normal HTTPS page loads.
3. An independent IP-check equals `SERVER_IP` in global/proxy mode.
4. Repeat once using cellular data.
5. Turn the tunnel off and confirm direct networking returns.
