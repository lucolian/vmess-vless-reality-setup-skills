# AWS EC2 beginner path for a phone destination

Create a server only when no working VMess server exists. Use accurate AWS account data, enable root MFA, prefer a non-root daily administrator, and create a monthly AWS Budget email alert before launching. Never ask for credentials, MFA codes, billing details, or recovery data. Budgets alert; they do not automatically cap spending.

From **EC2 → Instances → Launch instances**, select the desired region and use Ubuntu Server 24.04 LTS x86_64, a small burstable instance appropriate for testing, SSH-key authentication, public IPv4, and about 8 GiB gp3 storage. Availability, free-tier eligibility and price vary by account/region; inspect the current estimate and public-IPv4, storage, snapshot and outbound-traffic prices.

Create inbound rules:

| Type | Port | Source |
|---|---:|---|
| SSH | TCP 22 | Current administrator public IP as `/32` |
| Custom TCP | TCP 16823 | Intended phone sources; roaming phones commonly require `0.0.0.0/0` |

Never expose SSH to everyone. Explain that a world-open service port is scannable. If an administrator VPN changes, update the SSH **My IP** rule before assuming the VM failed.

Use an Elastic IP only when a stable address is needed. It can be billable and must be released during retirement. Store the `.pem` privately and connect as `ubuntu`:

```powershell
ssh -i "C:\PRIVATE\PATH\server-key.pem" ubuntu@SERVER_IP
```

or on macOS:

```bash
chmod 400 /private/path/server-key.pem
ssh -i /private/path/server-key.pem ubuntu@SERVER_IP
```

Do not continue to Xray until `cat /etc/os-release` works on the remote Ubuntu prompt.

On permanent retirement: disable the phone tunnel, terminate the instance, release its Elastic IP, delete unused volumes/snapshots, and remove unshared firewall/key resources. Stopping alone is not full cleanup.

Official references:

- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-security-group.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html
- https://docs.aws.amazon.com/cost-management/latest/userguide/create-cost-budget.html
