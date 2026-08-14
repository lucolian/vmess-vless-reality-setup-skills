# AWS EC2 beginner path

Use this only when the user does not already have a suitable Ubuntu server. Console labels and prices change; verify the current AWS page.

## Account and billing safety

Register an AWS Global account with accurate information. Never request passwords, MFA codes, payment data, access keys, or recovery information. Enable root-user MFA, use a non-root IAM Identity Center or IAM administrator for daily work when practical, and create a monthly cost budget with email alerts under **Billing and Cost Management → Budgets**. Alerts may be delayed and do not cap charges.

Inspect the live estimate for compute, EBS, public IPv4/Elastic IP, snapshots, and outbound transfer. Never promise a free deployment.

## Launch Ubuntu

Under **EC2 → Instances → Launch instances**, choose the required region, then:

```text
Name: non-personal label such as proxy-ubuntu
Image: Ubuntu Server 24.04 LTS
Architecture: 64-bit (x86), unless deliberately using ARM
Instance: small burstable size appropriate for personal testing
Key pair: SSH key; download and protect the private key
Network: a public subnet with auto-assign public IPv4 enabled
Storage: 8 GiB gp3 is a reasonable small baseline
```

Malaysia/Windows and Singapore/macOS are proven examples in this skill group; choose a region based on desired exit location, latency, availability, policy and current price.

## Security group

Allow only:

| Type | Protocol/port | Source |
|---|---|---|
| SSH | TCP 22 | Current administrator public IP as `/32` |
| HTTPS/custom TCP | TCP 443 | Intended clients; often `0.0.0.0/0` for a roaming laptop |

The port-443 rule permits the Xray listener; it does not turn the service into ordinary HTTPS. Do not open 80, UDP, All traffic, or world-open SSH. Warn that 443 exposed to everyone will be scanned.

If an existing VPN is disconnected or switched, its public IP may change and the SSH `/32` rule may stop matching. Update **My IP** before rebuilding anything.

## Address and SSH

An auto-assigned IPv4 can change after stop/start. Associate an Elastic IP only when clients need a stable address; explain current charges and release it at retirement. Record it privately as `SERVER_IP`.

Windows PowerShell:

```powershell
ssh -i "C:\PRIVATE\PATH\server-key.pem" ubuntu@SERVER_IP
```

macOS Terminal:

```bash
chmod 400 /private/path/server-key.pem
ssh -i /private/path/server-key.pem ubuntu@SERVER_IP
```

Verify the host fingerprint for the intended new VM when possible. Do not continue until the remote prompt works and `cat /etc/os-release` reports Ubuntu.

## Retirement

Clear the computer system proxy first. Then terminate the instance, release its Elastic IP, delete unused EBS volumes and snapshots, and remove only unshared key pairs/security groups. Review Billing/Cost Explorer afterward. Stop is not delete.

Official references:

- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-security-group.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html
- https://docs.aws.amazon.com/cost-management/latest/userguide/create-cost-budget.html
