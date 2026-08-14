---
name: setup-vless-reality-computer
description: Set up, validate, and troubleshoot VLESS with XTLS Vision and REALITY for a desktop or laptop. Use when the destination device is a Windows PC or Mac and the user needs an Ubuntu Xray server on AWS EC2, another VPS, or Azure; prioritize AWS EC2 plus Ubuntu, then other Ubuntu VPS providers, then Azure, and prioritize Windows v2rayN before macOS.
---

# Set up VLESS + REALITY on a computer

Guide a beginner through one checkpoint at a time. Do not dump the entire installation at once. At each checkpoint, state what success looks like. Ask whether a working VLESS + REALITY server already exists. If yes, skip server creation and collect every client value listed below. If no, build the server using the requested priority.

## Priorities

1. Server: AWS EC2 + Ubuntu; other Ubuntu VPS; Azure Ubuntu VM.
2. Client: Windows with v2rayN; macOS with the official v2rayN macOS build.

Read [references/aws-ec2-beginner.md](references/aws-ec2-beginner.md) for a first AWS deployment, [references/providers.md](references/providers.md) for provider differences, [references/workflow.md](references/workflow.md) for Xray and clients, and [references/verification-recovery.md](references/verification-recovery.md) for validation, target/credential changes, diagnosis, and cleanup.

## Practically proven routes

- AWS → Ubuntu 24.04 x86_64 → VLESS + REALITY + XTLS Vision over raw/TCP 443 → Windows v2rayN with Xray core.
- AWS → Ubuntu → the same protocol stack on 443 → macOS v2rayN.

The records include valid configuration, active service/listener, real traffic and matching server-region exit IP. The Windows record also includes an HTTP-success request through v2rayN's local mixed proxy and a corroborating Xray outbound log. Present these as proven examples, not guarantees. Do not claim Azure, another VPS, or another client/transport is personally proven.

## Beginner workflow

1. Preflight and protect the cloud account; set a budget alert before launching resources.
2. Create Ubuntu with SSH-key access, TCP 22 restricted to the current administrator IP, and TCP 443 allowed for clients.
3. Reach an Ubuntu prompt; inspect and back up any existing Xray/V2Ray state.
4. Install current Xray; test a REALITY target from the server; generate UUID, X25519 values, and short ID.
5. Write the full config; validate before restart; verify service and listener.
6. Enter every field into v2rayN; make the intended row truly active; start with TUN off and system proxy on.
7. Prove a real HTTPS request and matching exit IP; clear the system proxy and prove direct recovery.
8. Give a private value sheet and maintenance/retirement instructions.

## Required value sheet

Keep these names distinct:

```text
SERVER_IP
PORT
UUID
FLOW=xtls-rprx-vision
TRANSPORT=raw
SECURITY=reality
SERVER_NAME
SERVER_PRIVATE_KEY       server only; never put in client
CLIENT_REALITY_PASSWORD derived public key; some UIs call it Public key
SHORT_ID
FINGERPRINT=chrome
```

The official current Xray JSON field for the client-held public key is `password`; many GUIs and older references label the same value `Public key`. Never substitute the server private key.

## Guardrails

- Confirm authorization and compliance with law, provider terms, and network policy.
- Treat the SSH key, UUID, server private key, client REALITY password/public key, short ID, and share link as secrets. Redact them.
- Use SSH keys and restrict TCP 22 to the administrator IP.
- Warn before asking the user to disconnect or change an existing VPN. A VPN change can alter the public IP, terminate SSH, invalidate the port-22 allowlist, or conflict with proxy/TUN routing.
- Detect and back up existing Xray/V2Ray configuration. Do not overwrite an unknown working server.
- Select and test a suitable REALITY target instead of copying an arbitrary popular domain. Read the target-selection rules in the workflow.
- Own the REALITY-target decision: run the target test, interpret the result, explain the selection briefly, and ask the beginner only for relevant policy/region preferences. Do not ask a beginner to decide whether raw TLS output is suitable.
- For AWS, recommend root-account MFA, a non-root daily administrator, and a billing budget/usage alert. Never ask for MFA codes, root credentials, or recovery data.
- Explain cloud compute, traffic, storage, snapshot, and public-IP charges before creation. State that stopping a VM may not stop all charges and give complete cleanup steps.
- Use current Xray and current clients; do not lower REALITY minimum-client compatibility merely to accommodate an obsolete app without explaining the security/fingerprinting tradeoff.
- Read the installed Xray version and use its matching official schema. If validation rejects a field, interpret the exact error and repair the configuration; never ask a beginner to choose between `users`/`method` and legacy `clients`/`network` unaided.
- Never promise AWS Free Tier eligibility or a fixed price. Verify current account, region, public-IPv4, storage and traffic pricing.
- Preserve an already-working VMess inbound during migration unless the user explicitly wants it removed. A same-machine fallback is useful while VLESS credentials or targets change.

## Completion standard

Require a passing config test, active service, TCP listener, matching cloud/Ubuntu firewall, successful real web traffic on Windows or Mac, and an exit IP matching the server. On a computer using system proxy, also prove that clearing the system proxy restores direct browsing. Diagnose by layer and change one variable at a time.
