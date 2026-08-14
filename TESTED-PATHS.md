# Tested paths and acceptance standard

The evidence below concerns network deployment outcomes. It does not prove installation or behavior in every agent host. Record agent-host compatibility separately from protocol and client acceptance.

## Practically proven

The following anonymized routes were completed end to end in the owner's practical records:

| Server region and OS | Protocol | Client | Evidence obtained |
|---|---|---|---|
| AWS, Ubuntu 24.04 x86_64 | VMess over raw/TCP on a nonstandard port | Windows v2rayN | Valid server configuration, active service and listener, reachable port, real web traffic, and AWS exit IP |
| AWS, Ubuntu 24.04 x86_64 | VLESS + REALITY + XTLS Vision over raw/TCP 443 | Windows v2rayN with Xray core | `xray run -test` passed, service and listener were active, a request through the local mixed proxy returned HTTP success, server logs showed the outbound request, and the exit IP matched the server |
| AWS, Ubuntu | VLESS + REALITY + XTLS Vision over raw/TCP 443 | macOS v2rayN | Server checks passed and real traffic worked with the system proxy, expected Singapore exit IP, and a successful clear-system-proxy recovery check |
| AWS, Ubuntu | VLESS + REALITY + XTLS Vision over raw/TCP 443 | iPhone with Shadowrocket | Added after the working computer route with a separate phone UUID; corrected REALITY/TLS fields, direct AWS profile with Proxy Pass empty, Proxy routing and fallback off; real phone traffic used the intended Malaysia server |

No actual addresses, credentials, local usernames, filenames, or account details from those records are included here.

## Documented, not claimed as personally proven

- VMess on iPhone or Android.
- VLESS + REALITY on Android or on an iPhone client other than Shadowrocket.
- Either protocol on Azure.
- Either protocol on a non-AWS Ubuntu VPS.
- VMess on macOS.
- Any different transport, port, operating-system release, client, routing mode, or cloud region not named above.

These routes use the same protocol requirements and provider controls, but a publication must not quietly upgrade them to “tested.”

## Definition of end-to-end success

A route is proven only after all of these pass:

1. The intended VM is running and its current public IP is recorded.
2. Cloud and Ubuntu firewalls allow the intended TCP port.
3. Xray configuration validation passes.
4. The Xray service is active and listening on the intended port.
5. The intended client profile—not merely a selected row—is active.
6. A normal HTTPS request succeeds through the client's local proxy or phone VPN interface.
7. An independent IP-check reports the server public IP.
8. The client or server log corroborates the connection without exposing secrets.
9. The relevant recovery check passes: clear the computer system proxy, or turn off the phone tunnel, and confirm direct networking returns.

A green latency number, an open TCP port, or an active service proves only one layer and is not enough.

## Adding another proven path

Record the date, cloud and region, Ubuntu and Xray versions, protocol/transport/port, client and version, network type, every acceptance result above, and any deviations. Redact credentials and personal data before committing the record. Test on a fresh client profile when possible.
