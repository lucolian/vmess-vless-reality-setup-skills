# VLESS + REALITY verification, recovery and maintenance

## Layered acceptance

1. Cloud: VM Running, `SERVER_IP` current, TCP 443 allowed.
2. Ubuntu firewall: UFW inactive or 443/tcp allowed.
3. Target: `xray tls ping SERVER_NAME` produces suitable TLS behavior from this server.
4. JSON: `sudo /usr/local/bin/xray run -test -config /usr/local/etc/xray/config.json` succeeds.
5. Service/listener: `systemctl is-active xray` and `sudo ss -lntp 'sport = :443'` succeed.
6. Client: active status shows intended `[VLESS]` profile and `:443` using Xray core.
7. Values: address, UUID, Vision flow, raw/TCP, reality, SNI, Chrome fingerprint, client public value and short ID match.
8. Local proxy: an HTTPS HEAD request through the actual v2rayN SOCKS/mixed port returns an HTTP response.
9. Egress: independent IP-check equals `SERVER_IP`; recent server/client logs corroborate traffic.
10. Recovery: clear system proxy, stop v2rayN, and prove direct browsing.

An open port or latency result alone is inconclusive.

## Common failures

| Symptom | Likely issue | Next action |
|---|---|---|
| SSH timeout after VPN switch | Port-22 `/32` mismatch | Update cloud **My IP** source |
| 443 unreachable | Firewall/listener/VM | Check security group/NSG, UFW, service, `ss` |
| 443 reachable, REALITY fails | Field mismatch | Compare SNI/target/serverNames, UUID, keys, short ID, flow, transport |
| Public key derivation fails | Public key passed to `x25519 -i` | Use the server private key locally on Ubuntu |
| Old VMess egress appears | VLESS row not active | Confirm bottom status, not row highlight |
| Works until v2rayN exits | Stale system proxy | Clear system proxy before quitting |
| One network only | Network filtering | Preserve config; compare hotspot/Wi-Fi |

## Roll back

Never restart after a failed config test. Correct the JSON or restore a private timestamped backup, validate it, restart, and recheck service/listener. Do not print the backup contents.

## Change only the REALITY target

1. Test the candidate using `xray tls ping` from the server.
2. Back up the config.
3. Change server `target` and every intended `serverNames` entry together.
4. Validate before restart; restart and verify service/listener.
5. Change only client SNI/server name.
6. Repeat real-traffic acceptance.

Do not rotate UUID, X25519 values, short ID, address, port or flow during a target-only change.

## Rotate credentials safely

Keep a known-working fallback when possible. Back up; generate a new UUID, X25519 pair and short ID; replace the server UUID/private key/short ID; validate before restart; restart and verify; immediately update client UUID, client public value and short ID; perform the full acceptance test. Return log level to `warning`. Remove the old credentials only after every intended device succeeds.

Use one UUID per device under the server `users` list. The REALITY keypair/SNI/short ID may be shared so one lost device UUID can be revoked independently.

## Updates, reboot and retirement

Record Xray/client versions and back up before updates. After an update or controlled reboot, repeat JSON, active service, listener and real-traffic checks. On retirement, clear the system proxy, delete private profiles as intended, terminate the VM, release static IPs, remove unused disks/snapshots, and review billing.
