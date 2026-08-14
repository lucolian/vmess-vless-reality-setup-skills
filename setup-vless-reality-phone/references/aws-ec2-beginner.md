# AWS EC2 beginner path for a phone destination

Create a VM only when no working VLESS + REALITY server exists. Register with accurate account/billing information, enable root MFA, prefer a non-root daily administrator, and create a monthly AWS Budget email alert before launch. Never ask for passwords, MFA codes, access keys, payment data, or recovery information. A budget alerts; it does not cap spending.

From **EC2 → Instances → Launch instances**, choose the desired region and use Ubuntu Server 24.04 LTS x86_64, a small burstable instance appropriate for testing, SSH-key authentication, public IPv4, and about 8 GiB gp3. Inspect the current estimate and public-IPv4, storage, snapshot and outbound-transfer prices; never promise free-tier eligibility.

Create only these inbound rules:

| Type | Port | Source |
|---|---:|---|
| SSH | TCP 22 | Current administrator public IP as `/32` |
| HTTPS/custom TCP | TCP 443 | Intended clients; roaming phones usually require `0.0.0.0/0` |

Do not open 80, UDP, All traffic, or world-open SSH. The 443 rule makes the listener internet-scannable. If an administrator VPN/network changes, update the SSH **My IP** source before diagnosing a timeout.

Use an Elastic IP only when a stable client address is required and release it during retirement. Protect the `.pem` and connect as `ubuntu`:

```powershell
ssh -i "C:\PRIVATE\PATH\server-key.pem" ubuntu@SERVER_IP
```

or:

```bash
chmod 400 /private/path/server-key.pem
ssh -i /private/path/server-key.pem ubuntu@SERVER_IP
```

Require a working Ubuntu prompt and `cat /etc/os-release` before Xray work.

For permanent cleanup, turn off the phone tunnel, terminate the VM, release the Elastic IP, delete unused volumes/snapshots, and remove unshared firewall/key resources. Stopping is not complete deletion.

Official references:

- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-security-group.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html
- https://docs.aws.amazon.com/cost-management/latest/userguide/create-cost-budget.html
