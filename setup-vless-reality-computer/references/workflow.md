# VLESS + REALITY server and computer clients

## Prepare and install

Create the private value sheet shown in `SKILL.md`; never paste completed secrets into public chat. Check Ubuntu, time, UFW, port 443, and existing services. If port 443 or Xray already exists, identify and back it up rather than overwriting it. Install current Xray from the official installer only after explaining the downloaded-script action:

```bash
cat /etc/os-release
sudo ss -lntp
timedatectl status
sudo ufw status verbose
systemctl list-unit-files | grep -E '^(xray|v2ray)' || true
sudo apt-get update
sudo apt-get install -y curl openssl
sudo bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
/usr/local/bin/xray version
```

Back up before editing:

```bash
sudo install -m 600 /usr/local/etc/xray/config.json "/usr/local/etc/xray/config.json.backup.$(date +%Y%m%d-%H%M%S)"
```

## Select a REALITY target

Choose a stable TLS 1.3 site reachable from the server, preferably in the same ASN/network environment as the server. Ensure its certificate accepts the chosen `SERVER_NAME`, and keep `target` and `serverNames` consistent. Avoid arbitrary CDN targets: failed REALITY authentication is forwarded to the target and can turn the server into an unwanted forwarder. Verify candidates with current Xray tooling, for example:

```bash
/usr/local/bin/xray tls ping TARGET_DOMAIN
```

Interpret the response behavior and SNI for the user; do not ask a beginner to judge raw TLS output. Do not continue until they are suitable. Use `TARGET_DOMAIN:443` for `target` and `TARGET_DOMAIN` for the single beginner `serverNames` entry.

For AWS Mac, `www.bing.com:443` with SNI `www.bing.com` is a practically verified example, not a universal default. Re-run `xray tls ping www.bing.com` from the actual server before choosing it. Never assume a target remains suitable forever.

## Generate credentials

```bash
/usr/local/bin/xray uuid
/usr/local/bin/xray x25519
openssl rand -hex 8
```

Record the generated private key only in the server config. Record the corresponding client value shown by current Xray; this is the public key and the current official client JSON calls it `password`. The 16-hex-character OpenSSL result is the short ID. To derive it again, pass the **server private key** to `xray x25519 -i`; never pass the client public value. In current output, copy `Password (PublicKey)` to the GUI Public key/Password field.

Back up the existing config. Substitute every uppercase sample value in this strict JSON. Current official Xray documentation uses `settings.users` and `streamSettings.method`; an older installed version may use legacy `clients`/`network`. The assistant must read `xray version`, consult that release's official schema when needed, run the config test, and interpret its exact error. Never tell the beginner to guess or mix field generations.

```json
{
  "log": {"loglevel": "warning"},
  "inbounds": [{
    "listen": "0.0.0.0",
    "port": 443,
    "protocol": "vless",
    "settings": {
      "users": [{
        "id": "UUID",
        "flow": "xtls-rprx-vision",
        "level": 0
      }],
      "decryption": "none"
    },
    "streamSettings": {
      "method": "raw",
      "security": "reality",
      "realitySettings": {
        "show": false,
        "target": "TARGET_DOMAIN:443",
        "xver": 0,
        "serverNames": ["TARGET_DOMAIN"],
        "privateKey": "SERVER_PRIVATE_KEY",
        "shortIds": ["SHORT_ID"]
      }
    },
    "tag": "vless-reality-in"
  }],
  "outbounds": [
    {"protocol": "freedom", "tag": "direct"},
    {"protocol": "blackhole", "tag": "blocked"}
  ],
  "routing": {
    "rules": [{
      "type": "field",
      "ip": ["geoip:private"],
      "outboundTag": "blocked"
    }]
  }
}
```

Validate before restart:

```bash
sudo /usr/local/bin/xray run -test -config /usr/local/etc/xray/config.json
sudo systemctl restart xray
sudo systemctl enable xray
systemctl is-active xray
sudo ss -lntp 'sport = :443'
sudo journalctl -u xray -n 100 --no-pager
```

If UFW is active, allow `443/tcp`; never enable it before allowing SSH.

After package upgrades or a controlled reboot, verify `systemctl is-active xray` and the port-443 listener again. A successful pre-reboot state does not prove automatic startup afterward.

## Windows first: v2rayN

Download from https://github.com/2dust/v2rayN/releases and add VLESS manually:

```text
Address: SERVER_IP
Port: 443
UUID/User ID: UUID
Flow: xtls-rprx-vision
Encryption: none
Transport: raw (older UI may say tcp)
Security: reality
SNI/Server name: TARGET_DOMAIN
Fingerprint: chrome
Public key/Reality password: CLIENT_REALITY_PASSWORD
Short ID: SHORT_ID
SpiderX: / or the client's default
Core: Xray
```

Do not enable insecure certificate checking and do not put `SERVER_PRIVATE_KEY` in the client. Save, select, then explicitly set the row active. Merely highlighting it is insufficient; the bottom/status area must show `[VLESS]`, the intended label and port 443.

For everyday use, prefer the system proxy with TUN off unless an application ignores normal proxy settings. The local mixed proxy is commonly `127.0.0.1:10808`; read the actual status. Test from PowerShell:

```powershell
curl.exe -I -x socks5h://127.0.0.1:10808 https://www.example.com/ --max-time 15
```

If using a mainland/private-destination bypass or whitelist routing profile, explain that local traffic stays direct, reducing latency and unnecessary cloud data transfer. For acceptance, ensure the IP-check request is proxied. Clear the system proxy before quitting v2rayN or returning to another VPN.

## Mac second: v2rayN

Use the current official macOS v2rayN build matching Intel x64 or Apple silicon. Enter the identical values and use this tested operating pattern:

1. Disconnect a conflicting VPN only after warning that this may change the SSH allowlisted IP.
2. Leave TUN off for normal use. TUN requires the Mac login/admin password, captures more applications, and may conflict with another VPN.
3. Choose **Set system proxy**. v2rayN normally exposes a local mixed proxy such as `127.0.0.1:10808`; applications honoring macOS proxy settings use it.
4. Validate a real webpage, exit IP, client logs, and a successful connection/latency result. Actual traffic remains the decisive test.
5. Before quitting v2rayN or reconnecting the old VPN, choose **Clear system proxy**. Otherwise macOS may keep pointing at a local proxy that is no longer running and browsing can fail.

Use **Do not change system proxy** only when applications are manually configured for the local proxy or another tool manages macOS proxy settings.

## Change a working REALITY target safely

Preserve the user's VPN state when possible. Test the new hostname with `xray tls ping`, back up the config, change both server-side values (`target` and `serverNames`), validate before restart, restart and verify service/listener, then change the client SNI/server name to the same hostname. Do not rotate the UUID, client REALITY password/public key, short ID, flow, address, or port as part of a target-only change.

## Diagnose

If SSH times out, first ask whether the network or VPN public IP changed and compare it with the port-22 `/32` rule. If TCP 443 is unreachable, inspect cloud/Ubuntu firewalls and the listener. If TCP works but REALITY fails or v2rayN shows latency `-1`, confirm Xray is active, port 443 is listening, and `target`, `serverNames`, and client SNI are identical; then compare UUID, flow, raw/TCP, fingerprint, client public-key/password value, and short ID. Check client/server logs without printing the config, import link, or secrets.

If browsing stops after v2rayN quits, reopen it and clear the system proxy or clear macOS/Windows proxy settings manually. This is a local proxy-state problem, not evidence that Xray credentials failed.

If v2rayN still shows `[VMess]` and a different port, the VLESS row is not active. If the client public value must be recovered, derive it from the private key stored on the server; never replace it with an unrelated key. When an older working VMess inbound exists, preserve it during diagnosis and change one VLESS field at a time.

Read [verification-recovery.md](verification-recovery.md) for the complete acceptance matrix, rollback and safe rotations.

Official references:

- https://xtls.github.io/en/config/inbounds/vless.html
- https://xtls.github.io/en/config/transports/reality.html
- https://xtls.github.io/en/config/transport.html
- https://github.com/XTLS/Xray-install
