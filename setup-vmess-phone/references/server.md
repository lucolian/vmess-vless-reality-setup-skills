# Create a basic VMess server only when needed

Prioritize AWS EC2 with Ubuntu, then another Ubuntu VPS, then Azure Ubuntu. For AWS, read [aws-ec2-beginner.md](aws-ec2-beginner.md). For another provider, create Ubuntu 24.04 LTS (22.04 if required), SSH-key access and public IPv4; allow TCP 22 from the administrator `/32` and TCP 16823 from intended phone networks. For Azure, create an Ubuntu VM with SSH public key, public IP, cost alert, and equivalent NSG rules. Check provider firewall/NSG and UFW separately.

Restricting a mobile carrier's changing source IP may be impractical. If the service port uses `0.0.0.0/0`, explain internet-wide exposure. Never expose SSH to everyone. On permanent retirement, delete/release the VM, disk, snapshot, static IP and related unused resources; stop/deallocate is incomplete.

Check time, port collision, firewall and existing installation:

```bash
cat /etc/os-release
timedatectl status
sudo ss -lntp
sudo ufw status verbose
systemctl list-unit-files | grep -E '^(xray|v2ray)' || true
```

VMess requires accurate UTC time within 120 seconds. Preserve any working configuration. Install Xray from its official project:

```bash
sudo apt-get update
sudo apt-get install -y curl
sudo bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
/usr/local/bin/xray uuid
```

Back up the current config, then use this complete JSON with the generated UUID. If the installed Xray rejects `users` or `method`, the assistant must read the installed version, consult its official version-matched schema, interpret the exact error and repair it before restart. Never make the beginner guess older field names.

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

This is VMess over raw/TCP without TLS; do not describe it as HTTPS. Validate before restart:

```bash
sudo /usr/local/bin/xray run -test -config /usr/local/etc/xray/config.json
sudo systemctl restart xray
sudo systemctl enable xray
systemctl is-active xray
sudo ss -lntp 'sport = :16823'
sudo journalctl -u xray -n 50 --no-pager
```

If UFW is active, allow `16823/tcp`; do not enable UFW until SSH is allowed. Preserve address, port and UUID privately. For a separate phone credential, add another object under `settings.users`, validate, then restart. Merely replacing a UUID in a link does not authorize it.

After a controlled reboot, repeat service/listener checks. For rollback, restore a timestamped backup only after validating it. Rotate one device UUID at a time and retest real phone traffic.

For complete current configuration fields, use:

- https://xtls.github.io/en/config/inbounds/vmess.html
- https://xtls.github.io/en/config/transport.html
- https://github.com/XTLS/Xray-install
