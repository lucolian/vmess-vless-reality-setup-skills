---
name: setup-vmess-computer
description: Set up, validate, and troubleshoot VMess for a desktop or laptop. Use when the user's destination device is a Windows PC or Mac and they need an Ubuntu Xray server on AWS EC2, another VPS, or Azure; prioritize AWS EC2 plus Ubuntu, then other Ubuntu VPS providers, then Azure, and prioritize Windows v2rayN before macOS.
---

# Set up VMess on a computer

Guide a beginner through one checkpoint at a time. Do not dump the entire installation at once. At each checkpoint, state what success looks like and wait for the result when working interactively.

First ask whether a working VMess server already exists. If yes, skip cloud/server creation and collect its address, port, UUID, transport, encryption, TLS mode, and whether another device already works. If no, build it using the priority order below.

## Priorities

1. Server: AWS EC2 + Ubuntu; other Ubuntu VPS; Azure Ubuntu VM.
2. Client: Windows with v2rayN; macOS with the official v2rayN macOS build.

Read [references/aws-ec2-beginner.md](references/aws-ec2-beginner.md) for a first AWS deployment, [references/providers.md](references/providers.md) for other VPS/Azure or provider differences, and [references/workflow.md](references/workflow.md) for Xray and the client. Read [references/verification-recovery.md](references/verification-recovery.md) when validating, diagnosing, rotating credentials, or retiring the server.

## Practically proven route

The owner's anonymized practical record proves AWS → Ubuntu 24.04 x86_64 → VMess raw/TCP on port 16823 → Windows v2rayN end to end. It included a valid configuration, active service/listener, real web traffic, and an exit IP matching the server. Present this as a proven example, never as a guarantee. Do not claim that macOS, phone, Azure, another VPS, or another port has been personally proven.

## Beginner workflow

1. Preflight: confirm authorization, region goal, budget, client OS/architecture, existing VPN, and whether port 16823 is already used.
2. Cloud: enable account protections and cost alerts, create Ubuntu with an SSH key, restrict SSH, and open only the chosen VMess TCP port.
3. SSH: prove a command prompt on Ubuntu before touching Xray.
4. Server: inspect and back up existing state, install Xray from its official project, generate a fresh UUID, write the full config, validate, then restart.
5. Client: install current official v2rayN, create the VMess profile, select it, make it active, and deliberately choose the proxy mode.
6. Verification: prove service, listener, reachability, real HTTPS traffic, matching exit IP, and system-proxy recovery.
7. Handoff: give the user the private value sheet, backup/recovery commands, update warning, and complete cloud cleanup checklist.

## Guardrails

- Confirm the user is authorized and their use follows law, provider terms, and network policy.
- Treat SSH keys, UUIDs, and share links as secrets. Redact them from screenshots and public issues.
- Use SSH keys; restrict SSH port 22 to the user's public IP.
- Detect and back up existing Xray/V2Ray configuration before any replacement. Do not overwrite an unknown working server.
- Explain that this basic VMess configuration has no TLS and must not be described as HTTPS.
- Explain cloud compute, traffic, storage, and public-IP charges.
- Never promise AWS Free Tier eligibility or a fixed price. Verify the user's account/region and show how to inspect estimated monthly cost.
- Warn before changing an existing VPN: doing so can change the SSH source IP and invalidate a `/32` rule.
- Keep VMess separate from VLESS + REALITY. Do not silently convert protocols while diagnosing.
- Read the installed Xray version and select its matching official configuration schema. Interpret config-test errors and repair them; never ask a beginner to decide between current `users`/`method` fields and legacy `clients`/`network` fields unaided.

## Completion standard

Require all of these before reporting success:

- Xray configuration test passes.
- Xray is active and listening on the selected TCP port.
- Cloud and Ubuntu firewalls allow the same port.
- The intended v2rayN profile is shown as active in the status area and loads real web traffic through the server.
- An IP-check site reports the server's public IP.
- Clearing the system proxy restores direct browsing after v2rayN is stopped.

Troubleshoot in this order: public IP and VM state; cloud firewall; Ubuntu firewall; Xray config/service/listener; exact client fields; system clock; local network. Change one variable at a time and never broadly expose SSH as a shortcut.
