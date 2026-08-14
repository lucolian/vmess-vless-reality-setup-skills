# Create VLESS + REALITY server only when needed

Prioritize AWS EC2 + Ubuntu, then another Ubuntu VPS, then Azure Ubuntu.

For a beginner AWS launch, read [aws-ec2-beginner.md](aws-ec2-beginner.md). For another Ubuntu VPS, inspect current terms, price, outbound transfer, deletion behavior, public-IP stability, port policy and console recovery before paying. For Azure, enable MFA/cost alerts and create Ubuntu with SSH key, public IP, TCP 22 restricted to the administrator `/32`, and TCP 443 for clients; check NSG priorities and UFW separately.

## AWS first

Recommend root-account MFA, a non-root IAM Identity Center or IAM administrator for daily work, and a billing budget/usage alert. Never ask for AWS passwords, recovery information, or MFA codes.

Use Ubuntu 22.04 or 24.04 LTS x86_64, public IPv4, and an SSH key. A tested small personal baseline is a burstable micro instance with 8 GiB gp3 storage, but availability and price vary. In the security group:

- Allow TCP 22 only from the administrator's current public or VPN IP as `/32`; never expose SSH to everyone.
- Allow TCP 443 for phones that move between Wi-Fi and cellular. `0.0.0.0/0` is often the practical source, but explain that the listener will be scanned from the internet.
- Do not open port 80; this setup does not require it.

Warn before any administrator VPN/network change: it can change the public IP and make SSH time out until the port-22 rule is updated to the new **My IP** `/32`.

For another Ubuntu VPS or Azure, apply the equivalent provider firewall/NSG rules, stable-public-IP decision, SSH-key protection, and billing alert. Check the Ubuntu firewall separately.

## Cost warning

Cloud charges can include compute runtime, public/static IPv4, storage, snapshots, and outbound data transfer. Stopping a VM may not stop all charges. On permanent retirement, terminate/delete the VM, release its static public IP, delete unused disks/volumes and snapshots, and remove unused firewall rules/key pairs only after confirming they are not shared.

Install current Xray from the official installer after checking `timedatectl status`, `sudo ss -lntp`, `sudo ufw status verbose`, existing Xray/V2Ray, and backing up any current config. Choose a suitable stable TLS 1.3 REALITY target reachable from the server, preferably in the same ASN/network environment; verify it with `xray tls ping`. The assistant must interpret the output rather than asking the beginner to judge it. Keep server `target`, server `serverNames`, and phone SNI identical. Avoid arbitrary CDN targets because failed authentication traffic is forwarded to the target.

For AWS Mac, `www.bing.com:443` with SNI `www.bing.com` is a practically verified example, not a universal default. Re-test it from the new server before use.

Generate:

```bash
/usr/local/bin/xray uuid
/usr/local/bin/xray x25519
openssl rand -hex 8
```

Use this complete JSON after replacing every uppercase placeholder. Current official Xray documentation uses `users` and `method`; if an older installed Xray rejects them, the assistant must read the installed version, consult that exact release's official schema, interpret the config-test error and repair it. Never make the beginner choose field generations or mix schemas without a passing test.

```json
{
  "log": {"loglevel": "warning"},
  "inbounds": [{
    "listen": "0.0.0.0",
    "port": 443,
    "protocol": "vless",
    "settings": {
      "users": [{"id": "PHONE_UUID", "flow": "xtls-rprx-vision", "level": 0}],
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
  "routing": {"rules": [{
    "type": "field",
    "ip": ["geoip:private"],
    "outboundTag": "blocked"
  }]}
}
```

The phone receives the corresponding derived public value, which current official Xray client JSON calls `password`; it never receives the private key. If deriving again, run `xray x25519 -i` with the **server private key**, not the client public value, and copy `Password (PublicKey)`.

Validate before restart:

```bash
sudo /usr/local/bin/xray run -test -config /usr/local/etc/xray/config.json
sudo systemctl restart xray
systemctl is-active xray
sudo ss -lntp 'sport = :443'
sudo journalctl -u xray -n 100 --no-pager
```

If UFW is active, allow `443/tcp`; do not enable it before allowing SSH.

After package upgrades or a controlled reboot, recheck that Xray starts automatically and still listens on 443. If a share link is generated on the server, do not print it into chat; protect any temporary file with mode `600` and delete it after private import when it is no longer needed.

For a second device, add a second `users` object with its own UUID, validate before restart, then configure that phone. Do not replace a link UUID without server authorization. Keep a known working computer/VMess path during changes when available.

## Change only the REALITY target

Test the new hostname, back up the config, change server `target` and `serverNames`, validate before restart, restart and verify service/listener, then change the phone SNI to exactly the same hostname. Do not also rotate the UUID, client public-key/password, short ID, flow, address, or port.

Official references:

- https://xtls.github.io/en/config/inbounds/vless.html
- https://xtls.github.io/en/config/transports/reality.html
- https://xtls.github.io/en/config/transport.html
- https://github.com/XTLS/Xray-install
