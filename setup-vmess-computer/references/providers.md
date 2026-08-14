# Ubuntu server providers

## AWS EC2 first

Use [aws-ec2-beginner.md](aws-ec2-beginner.md). Do not repeat cloud creation when a suitable working server exists.

Official guidance:

- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-security-group.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html

## Other Ubuntu VPS second

Choose a provider and region only after checking current price, terms, public-IPv4 availability, outbound transfer, deletion behavior, and whether the desired port is permitted. Create Ubuntu 24.04 LTS (22.04 remains acceptable when required) with an SSH key and public IPv4.

Before Xray, confirm:

```text
provider firewall: TCP 22 from administrator /32
provider firewall: TCP 16823 from intended clients
login user: documented by provider (often ubuntu or root)
public IP: stable or clearly documented as changeable
console/rescue access: known before editing firewall/SSH
```

Then connect by SSH, run `cat /etc/os-release`, and check `sudo ufw status verbose`. Both the provider firewall and Ubuntu firewall must permit the service. Stopping may not delete storage/reserved IPs; follow the provider's complete deletion procedure.

## Azure third

Before creation, enable MFA and a budget/cost alert. In **Virtual machines → Create → Azure virtual machine**, select the required subscription/resource group/region, Ubuntu 24.04 LTS, a small general-purpose size, SSH public key, a non-personal administrator username, and a public IP. On **Networking**, use an NSG with TCP 22 only from the current administrator `/32`. Add TCP 16823 from intended clients after creation. Check NSG rule priorities so a deny does not override the allow.

Choose a static public IP only when a stable address is required and explain current charges. Connect with:

```text
ssh -i PRIVATE_KEY ADMIN_USER@SERVER_IP
```

Confirm Ubuntu before installing. On retirement, delete the VM and inspect/delete its disk, snapshots, NIC, public IP, NSG and resource group as appropriate; deallocating is not the same as deleting all billable resources.

Official guidance:

- https://learn.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-portal
- https://learn.microsoft.com/en-us/azure/virtual-machines/linux-vm-connect
- https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-acm-create-budgets
