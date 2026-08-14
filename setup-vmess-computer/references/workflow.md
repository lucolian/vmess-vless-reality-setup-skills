# VMess server and computer clients

## Server

Keep a private value sheet:

```text
SERVER_IP=
PORT=16823
UUID=
TRANSPORT=raw
TLS=none
```

Do not paste the completed sheet into public chat or GitHub. Check Ubuntu, time synchronization, port collisions, and existing services:

```bash
cat /etc/os-release
timedatectl status
sudo ss -lntp
systemctl list-unit-files | grep -E '^(xray|v2ray)' || true
sudo ufw status verbose
```

VMess authentication depends on accurate UTC time; both server and client must be within 120 seconds of actual time. If another Xray/V2Ray service or port 16823 exists, stop and identify it. Never overwrite an unknown working config.

Install current Xray using the official installer after explaining the downloaded-script action:

```bash
sudo apt-get update
sudo apt-get install -y curl
sudo bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
/usr/local/bin/xray version
```

Generate a UUID with `/usr/local/bin/xray uuid`. Back up before editing:

```bash
sudo install -m 600 /usr/local/etc/xray/config.json "/usr/local/etc/xray/config.json.backup.$(date +%Y%m%d-%H%M%S)"
sudo nano /usr/local/etc/xray/config.json
```

Substitute only `UUID`; this deliberately uses no TLS. If the installed Xray rejects `users` or `method`, the assistant must record the version, consult its version-matched official schema, interpret the exact error and repair it before restart. Do not make the beginner choose legacy `clients` or `network` names or blindly mix schemas.

```json
{
  "log": {"loglevel": "warning"},
  "inbounds": [{
    "listen": "0.0.0.0",
    "port": 16823,
    "protocol": "vmess",
    "settings": {"users": [{"id": "UUID", "level": 0}]},
    "streamSettings": {"method": "raw", "security": "none"},
    "tag": "vmess-in"
  }],
  "outbounds": [{"protocol": "freedom", "tag": "direct"}]
}
```

In `nano`, save with Ctrl+O, Enter, exit with Ctrl+X. Do not paste a trailing explanation into JSON.

Validate before restart:

```bash
sudo /usr/local/bin/xray run -test -config /usr/local/etc/xray/config.json
sudo systemctl restart xray
sudo systemctl enable xray
systemctl is-active xray
sudo ss -lntp 'sport = :16823'
sudo journalctl -u xray -n 50 --no-pager
```

If UFW is active, use `sudo ufw allow 16823/tcp`. Do not enable UFW remotely until `OpenSSH` or 22/tcp is allowed. After a controlled reboot, repeat the active/listener checks to prove automatic startup.

## Windows first: v2rayN

Download only from https://github.com/2dust/v2rayN/releases. Select the official Windows package matching x64/ARM64; extract a ZIP to a private writable folder when using the portable build. Do not use download mirrors.

Add a VMess server manually. Menu wording changes; locate **Add VMess server** or the equivalent:

```text
Address: server public IPv4
Port: 16823
User ID: UUID
Alter ID: 0 if shown
Encryption: auto
Transport: raw (older UI may say tcp)
TLS/Security: none
Core: Xray
```

Save it, select it, and use the command that sets it as the active server. Merely highlighting a row may not activate it; confirm the bottom/status area names `[VMess]`, the correct label and port. Start with TUN off and **Set system proxy**. The local mixed proxy is commonly `127.0.0.1:10808`, but read the actual status.

From PowerShell, test through the displayed local SOCKS proxy:

```powershell
curl.exe -I -x socks5h://127.0.0.1:10808 https://www.example.com/ --max-time 15
```

Then load a normal site and an independent IP-check in the browser. The IP must match `SERVER_IP`. A latency-only failure is inconclusive.

## Mac second: v2rayN

Use the official macOS build from the same release page and select Intel x64 or Apple silicon ARM64 correctly. Check the release notes for current minimum macOS and installation warnings; do not bypass macOS security for a file from an unverified source. Enter identical fields, use Xray core, leave TUN off initially, and select **Set system proxy**. Validate real traffic and exit IP. Before quitting, choose **Clear system proxy** and prove direct browsing works.

## Diagnose

From Windows use `Test-NetConnection SERVER_IP -Port 16823`. On Ubuntu use the config test, `systemctl is-active xray`, `ss -lntp`, UFW status, time status, and recent journal. TCP reachability alone does not prove the UUID/client mapping. If Windows shows `[VLESS]` or another port in the status bar, activate the VMess row. If browsing fails only after v2rayN closes, clear the stale system proxy rather than rotating the UUID.

For complete validation, rollback, rotation and cleanup, read [verification-recovery.md](verification-recovery.md).

Official references:

- https://xtls.github.io/en/config/inbounds/vmess.html
- https://xtls.github.io/en/config/transport.html
- https://github.com/XTLS/Xray-install
