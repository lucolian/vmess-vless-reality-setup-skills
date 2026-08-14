# Ubuntu server providers

## AWS EC2 first

Use [aws-ec2-beginner.md](aws-ec2-beginner.md). It contains the required MFA, non-root administration, budget, field-by-field launch, SSH, cost, stable-IP and retirement steps.

- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-security-group.html
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html

## Other Ubuntu VPS second

Before paying, inspect current terms, price, public IPv4, outbound transfer, deletion behavior, port policy, console/rescue access and desired region. Choose Ubuntu 24.04 LTS (22.04 when required) with SSH-key access and public IPv4. Allow TCP 22 only from the administrator `/32` and TCP 443 from intended clients in the provider firewall. Determine the login username and whether the public IP is stable. Check both provider firewall and `sudo ufw status verbose`.

Warn that stopping a VPS may not delete storage or reserved-IP charges. Explain the provider's full deletion and static-IP release process before the user relies on stopping alone.

## Azure third

Enable MFA and a cost budget/alert. Under **Virtual machines → Create → Azure virtual machine**, select the required subscription/resource group/region, Ubuntu 24.04 LTS, a small general-purpose size, SSH public key, non-personal administrator username, and public IP. In the NSG, allow TCP 22 only from the administrator `/32` and TCP 443 from intended clients. Check priorities for overriding deny rules. Select static public-IP allocation only when needed and explain current charges.

Recommend Azure cost alerts. On retirement, delete or separately release the VM, disks, snapshots, network interface, and public IP as appropriate; stopping/deallocating is not equivalent to deleting every billable resource.

- https://learn.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-portal
- https://learn.microsoft.com/en-us/azure/virtual-machines/linux-vm-connect
- https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-acm-create-budgets
