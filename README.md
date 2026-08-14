# Usage-based proxy setup skills

This repository contains four portable Agent Skills that guide beginners through authorized VMess or VLESS + REALITY setup on Ubuntu cloud servers and computer or phone clients.

The portable core of each Skill is its `SKILL.md` and `references/` directory. A compatible agent must be able to load those files and follow their instructions. Installation and invocation differ between agent products, so this repository does not claim automatic compatibility with every agent or interface.

## Start here: beginner journey

1. Choose one Skill from the table below. Do not load all four for a single setup.
2. Download or clone this repository.
3. Install or import the complete selected Skill folder using your agent's documented Agent Skills method. Keep its directory structure intact, with `SKILL.md` at the folder root and `references/` beside it.
4. If the agent has no Skill installer, place the selected folder in a workspace the agent can read and ask it to load that folder's `SKILL.md` before helping you.
5. Tell the agent whether a working server already exists, which cloud and region you want, which device you use, and that you are a beginner.
6. Ask it to proceed one checkpoint at a time. Complete and report each result before continuing.
7. Never paste an SSH private key, cloud password or MFA code, UUID, REALITY key, short ID, import link, QR code, or unredacted screenshot into a chat or public issue.
8. Finish only after the Xray configuration, service, listener, and firewall checks pass; real HTTPS traffic works; the exit IP matches the server; and disconnect or clear-proxy recovery restores direct networking.

At each checkpoint, report only the result, for example: `Configuration OK; Xray active; port 443 listening; values redacted.` If a screenshot is necessary, crop it and cover IPs, UUIDs, keys, links, account identifiers, usernames, and local paths first.

## Choose a Skill

| Goal | Skill folder |
|---|---|
| VMess on Windows or macOS | `setup-vmess-computer` |
| VMess on iPhone or Android | `setup-vmess-phone` |
| VLESS + REALITY on Windows or macOS | `setup-vless-reality-computer` |
| VLESS + REALITY on iPhone or Android | `setup-vless-reality-phone` |

Use this selection guidance:

- Starting from nothing on Windows or macOS: prefer `setup-vless-reality-computer`.
- Reproducing the simpler proven Windows VMess route: use `setup-vmess-computer`.
- Adding a phone to a working computer connection: use the phone Skill matching the existing protocol and preserve the working server.
- Troubleshooting one connection: do not switch protocols until the current failure is understood.

## Copyable first prompts

VLESS + REALITY on a computer:

```text
Load and follow the setup-vless-reality-computer Skill. I am a complete
beginner and do not have a server yet. Help me create an AWS EC2 Ubuntu
server in my chosen region and use VLESS + REALITY with Windows v2rayN.
Guide me one checkpoint at a time, wait for each result, and never ask me
to publish or paste secrets.
```

VMess on a computer:

```text
Load and follow the setup-vmess-computer Skill. I am a complete beginner
and do not have a server yet. Help me create an AWS EC2 Ubuntu server in
my chosen region and use VMess with Windows v2rayN. Guide me one checkpoint
at a time, wait for each result, and never ask me to publish or paste secrets.
```

VLESS + REALITY on a phone:

```text
Load and follow the setup-vless-reality-phone Skill. My VLESS + REALITY
server already works on my computer. Preserve that working server, add a
separate UUID for my iPhone, and help me configure Shadowrocket. Guide me
one checkpoint at a time and test both Wi-Fi and cellular data.
```

VMess on a phone:

```text
Load and follow the setup-vmess-phone Skill. My VMess server already works
on my computer. Preserve that working server, add a separate UUID for my
phone, and guide me through the phone client one checkpoint at a time.
Treat this phone route as documented, not personally proven, until the full
acceptance test passes.
```

## Compatibility

- Each Skill follows the common `SKILL.md` plus bundled-resources structure.
- `SKILL.md` and `references/` contain the functional, vendor-neutral workflow.
- `agents/openai.yaml` supplies optional OpenAI interface metadata. Other agents may ignore it without losing the workflow.
- A host that supports Agent Skills may discover or import the folders directly. Other hosts may require the user to attach the selected folder or place it in an agent-readable workspace.
- Syntax for explicitly invoking a Skill is host-specific. Use the host's documented selector or mention the Skill by name and ask the agent to load its `SKILL.md`.
- The deployment routes below have practical evidence, but the Skill collection has not been tested in every agent product. Report host-specific compatibility results without upgrading assumptions to guarantees.

## Scope and priorities

The server order is AWS EC2 with Ubuntu first, another Ubuntu VPS second, and Azure Ubuntu third. Computer Skills use Windows v2rayN first and macOS second. Phone Skills use iPhone and Shadowrocket first, other iPhone clients second, and Android third.

These Skills are for systems the user owns or is authorized to administer. Users remain responsible for applicable laws, cloud-provider terms, network policy, and charges.

## What a beginner needs

- An email address, telephone number, and payment method accepted by the chosen cloud provider.
- A Windows PC or Mac for cloud setup and SSH administration.
- A protected place for the SSH private key and generated proxy credentials.
- Permission to install a proxy client and create a system or VPN network configuration.
- Patience to complete each checkpoint instead of changing several settings at once.

The Skills teach AWS safety and billing setup, exact EC2 choices, SSH, Xray installation and configuration, cloud and Ubuntu firewall checks, client entry, end-to-end validation, recovery, and complete cloud cleanup. Prices, free-tier eligibility, regional availability, software schemas, and app interfaces change. The agent must verify current provider documentation and official client releases at use time rather than promise a fixed price or identical button labels.

## Evidence status

Four routes are practically proven from the owner's anonymized deployment records:

1. AWS -> Ubuntu -> VMess -> Windows v2rayN.
2. AWS -> Ubuntu -> VLESS + REALITY -> Windows v2rayN.
3. AWS -> Ubuntu -> VLESS + REALITY -> macOS v2rayN.
4. AWS -> Ubuntu -> VLESS + REALITY -> iPhone Shadowrocket, added after the working computer setup with a separate phone UUID.

Android, VMess on a phone, Azure, and other-VPS routes are technically documented but are not represented as personally end-to-end proven. See [TESTED-PATHS.md](TESTED-PATHS.md).

## Repository layout

```text
setup-vmess-computer/
setup-vmess-phone/
setup-vless-reality-computer/
setup-vless-reality-phone/
LICENSE
README.md
SECURITY.md
TESTED-PATHS.md
```

Each Skill directory is self-contained and contains `SKILL.md`, optional host metadata under `agents/`, and focused reference material. Repository-level documents explain usage, evidence, and public-safety expectations.

## Public-safety rule

Never publish real IP addresses, UUIDs, REALITY private or public values, short IDs, import links or QR codes, SSH private keys, usernames, local paths, account IDs, billing details, screenshots with identifiers, or unredacted logs. All committed examples use placeholders. See [SECURITY.md](SECURITY.md).

## Limits

The guides intentionally do not promise anonymity, a residential IP, uninterrupted access, compatibility with every agent host, or suitability for accounts that forbid proxies or cloud-hosted IPs. An EC2 or VPS public IP is a datacenter IP. Routing rules can also allow some traffic to go direct, so the user must validate the actual application and exit IP they care about.
