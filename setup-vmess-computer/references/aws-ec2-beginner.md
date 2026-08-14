# AWS EC2 beginner path

Use this only when the user does not already have a suitable Ubuntu server. Console labels change; verify the current AWS page rather than guessing.

## 1. Protect the account before creating resources

1. Register an AWS Global account with accurate identity and billing information. Never ask the user to reveal passwords, payment details, recovery information, access keys, or MFA codes.
2. Enable MFA for the root user. Use a non-root IAM Identity Center or IAM administrator for routine work when practical.
3. Open **Billing and Cost Management → Budgets → Create budget**. Create a monthly cost budget with email alerts below and at the user's real spending limit. Explain that alerts are delayed and do not cap charges automatically.
4. Open the current AWS pricing/estimate shown by the launch page. Confirm instance, storage, public IPv4, snapshot and outbound-transfer costs. Never promise “free.”

Checkpoint: MFA is enabled, the user can access a non-root administrator, and a budget notification is configured.

## 2. Choose the region and launch Ubuntu

Use the region selector before starting. Malaysia and Singapore are proven examples for this skill group, but the correct region depends on required exit location, latency, availability, policy, and price.

From **EC2 → Instances → Launch instances**, use:

```text
Name: a non-personal label such as proxy-ubuntu
Application and OS image: Ubuntu Server 24.04 LTS
Architecture: 64-bit (x86), unless the client knowingly chooses ARM
Instance type: a small burstable instance suitable for personal testing
Key pair: create/select an SSH key; download the private key once
Network: default VPC/subnet is acceptable for a first single VM
Auto-assign public IP: enabled
Storage: 8 GiB gp3 is a reasonable small starting point
```

Do not put a real name, email, phone number, or credential in resource names or tags. Store the `.pem` file in a private folder; never upload or commit it.

## 3. Security group

Create an inbound security group with only:

| Type | Protocol/port | Source |
|---|---|---|
| SSH | TCP 22 | **My IP**, a single `/32` administrator address |
| Custom TCP | TCP 16823 | Intended client sources; `0.0.0.0/0` only when roaming clients require it |

Do not open All traffic, do not open UDP, and never expose SSH to `0.0.0.0/0`. A world-open service port is internet-scannable. If the administrator uses a VPN, its current public IP—not the home IP—must match the SSH rule. Changing or disconnecting that VPN can break SSH until **My IP** is updated.

Checkpoint: the instance state is Running, status checks pass, and the security-group rules show 22/tcp plus 16823/tcp only.

## 4. Stable address decision

The automatically assigned public IPv4 can change after stop/start. If clients require a stable address, allocate an Elastic IP and associate it with this instance. Explain its current charge and release it when no longer needed. Merely disassociating it does not necessarily stop charges.

Checkpoint: record the chosen public IPv4 privately as `SERVER_IP`. Do not paste the real address into a public issue.

## 5. Connect from Windows or macOS

Windows PowerShell:

```powershell
ssh -i "C:\PRIVATE\PATH\server-key.pem" ubuntu@SERVER_IP
```

macOS Terminal:

```bash
chmod 400 /private/path/server-key.pem
ssh -i /private/path/server-key.pem ubuntu@SERVER_IP
```

On the first connection, compare the displayed host-key fingerprint with the EC2 console's instance fingerprint when available. Accept it only for the intended new instance. If permission errors mention the private key, fix local permissions; do not make the key public.

Checkpoint: the prompt begins with an Ubuntu account/host and `cat /etc/os-release` reports Ubuntu.

## 6. Retirement

Before termination, clear every client system proxy/VPN profile and preserve only the backups the user needs. Then terminate the EC2 instance, release the Elastic IP, delete unneeded EBS volumes and snapshots, and remove unused security groups/key pairs only after confirming they are not shared. Check **Billing/Cost Explorer** afterward. Stopping the instance is not complete cleanup.

Official references:

- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-security-group.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html
- https://docs.aws.amazon.com/cost-management/latest/userguide/create-cost-budget.html
