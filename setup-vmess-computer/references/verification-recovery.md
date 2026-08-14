# VMess verification, recovery and maintenance

## Layered verification

Run one layer at a time and stop at the first failure.

1. Cloud: instance is Running; `SERVER_IP` is current; inbound TCP 16823 exists.
2. Ubuntu firewall: `sudo ufw status verbose`; allow 16823/tcp if UFW is active.
3. Configuration: `sudo /usr/local/bin/xray run -test -config /usr/local/etc/xray/config.json`.
4. Service: `systemctl is-active xray`.
5. Listener: `sudo ss -lntp 'sport = :16823'`.
6. Remote reachability on Windows: `Test-NetConnection SERVER_IP -Port 16823`.
7. Client state: bottom/status area names the intended `[VMess]` profile and 16823.
8. Local-proxy request: run `curl.exe` through the actual local mixed-proxy port and require an HTTP response.
9. Browser: normal HTTPS works and an independent IP-check equals `SERVER_IP`.
10. Recovery: clear system proxy, stop v2rayN, and require direct browsing.

## Common failures

| Symptom | Most likely layer | Next check |
|---|---|---|
| SSH timeout after VPN change | SSH `/32` no longer matches | Update cloud port-22 source to current public IP |
| Port test fails | VM/firewall/listener | Instance state, security group/NSG, UFW, `ss` |
| Port works but proxy fails | Client fields/auth/time | UUID, raw/TCP, TLS none, Xray core, clocks |
| Highlighted row but old server used | Client activation | Bottom/status area, set selected server active |
| Browser fails after app closes | Stale system proxy | Reopen app, clear system proxy, then quit |
| Only one network works | Network filtering | Preserve credentials; compare Wi-Fi/hotspot |

## Roll back a failed server edit

Do not restart after a failed config test. Correct the JSON or restore a known backup:

```bash
sudo cp /usr/local/etc/xray/config.json.backup.TIMESTAMP /usr/local/etc/xray/config.json
sudo /usr/local/bin/xray run -test -config /usr/local/etc/xray/config.json
sudo systemctl restart xray
```

Replace `TIMESTAMP` with the actual private filename. Verify service and listener.

## Rotate a VMess UUID

Back up first. Generate `/usr/local/bin/xray uuid`, replace the user ID, validate before restart, restart and verify, immediately update every intended client, then perform the full real-traffic check. A device with the old UUID will stop working. For multiple devices, use one user/UUID per device so one can be revoked independently.

## Updates and reboot

Before an Xray/client update, record versions and keep a config backup. Read official release notes. After update and after a controlled Ubuntu reboot, repeat configuration, service, listener and real-traffic checks. Do not treat an update as successful merely because it installed.

## Retirement

Clear client system proxy settings first. Remove private profiles/backups as intended, terminate/delete the VM, release reserved/static public IPs, remove unneeded disks/snapshots, and inspect billing. Never publish the retired configuration as a troubleshooting example.
