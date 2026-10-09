# System-Admin

# Jr. System Administrator / Sr. Desktop Support — Interview Preparation

For this Pune-based role, focus on Windows Server, Active Directory, DNS, DHCP, Group Policy, LAN/WAN, firewall, backups, Linux basics, and troubleshooting scenarios.

Since you already work as a Technical Support Engineer, connect your answers to your real experience with Windows, Active Directory, Microsoft 365, ServiceNow, remote support, and network troubleshooting. The job asks for 3–5 years of experience, so explain your actual hands-on experience honestly and demonstrate your practical knowledge.

## 1. Tell me about yourself

Q1. Introduce yourself.

Answer:

"I'm a BE Information Technology graduate currently working as a Technical Support Engineer at Accel IT Services. My responsibilities include troubleshooting hardware and software issues, Windows OS support, Active Directory user management, Microsoft 365 support, network and VPN troubleshooting, and incident management through ServiceNow. I am looking to grow into system administration, particularly Windows Server, networking, security, and infrastructure management."

## 2. Windows Server and Active Directory

Q2. What is Windows Server?

Windows Server is a server operating system used to manage users, computers, applications, files, and network services centrally.

Q3. What is Active Directory (AD)?

Active Directory is Microsoft's directory service used to centrally manage users, computers, groups, authentication, and access permissions in a Windows domain.

Q4. What is a Domain Controller?

A Domain Controller is a server that runs Active Directory Domain Services and authenticates domain users and computers.

Q5. What is Group Policy (GPO)?

Group Policy allows administrators to apply centralized settings to users and computers.

Examples:

* Password policies

* USB restrictions

* Screen-lock settings

* Software deployment

* Windows security settings

Q6. What is the difference between a workgroup and a domain?

| Workgroup                       | Domain                            |
| ------------------------------- | --------------------------------- |
| Decentralized management        | Centralized management            |
| Accounts managed locally        | Domain accounts managed centrally |
| Suitable for small environments | Suitable for organizations        |

Q7. How do you unlock a locked AD account?

1. Open Active Directory Users and Computers.

2. Find the user account.

3. Open Properties.

4. Go to the Account tab.

5. Unlock the account, if the option is available.

6. Check why the account locked, such as an old password saved on a phone or service.

Q8. What is the difference between authentication and authorization?

* Authentication: Verifies who you are.

* Authorization: Determines what you are allowed to access.

Q9. What is the difference between a security group and a distribution group?

* Security group: Used to assign permissions and can also be used for email distribution.

* Distribution group: Used to distribute emails and does not itself grant resource permissions.

## 3. DNS and DHCP

Q10. What is DNS?

DNS converts domain names into IP addresses and helps computers locate services by name.

Example: A client queries DNS to find a server's IP address.

Q11. What is DHCP?

DHCP automatically assigns IP configuration to clients, including IP address, subnet mask, gateway, and DNS server.

Q12. What happens if DHCP stops working?

Clients may fail to obtain valid IP addresses. On Windows, an address beginning with `169.254.x.x` often indicates that DHCP assignment failed.

Troubleshooting:

1. Check cable or Wi-Fi connectivity.

2. Run `ipconfig /all`.

3. Check DHCP server and scope availability.

4. Verify VLAN, DHCP relay, and network connectivity as applicable.

5. Run `ipconfig /renew`.

Q13. How do you troubleshoot a DNS issue?

1. Run `ipconfig /all`.

2. Verify the configured DNS server.

3. Run `nslookup companydomain.local`.

4. Test connectivity to the DNS server.

5. Check DNS records and DNS service health.

Useful commands:

cmd

```
ipconfig /all
ipconfig /flushdns
nslookup servername
nslookup company.com
```

Q14. What is a DHCP scope?

A DHCP scope is a defined range of IP addresses that a DHCP server can lease to clients, along with associated network settings.

Q15. What is a DNS A record?

An A record maps a hostname to an IPv4 address. An AAAA record maps a hostname to an IPv6 address.

## 4. Networking: LAN, WAN, IP and Ports

Q16. What is the difference between a switch and a router?

| Switch                               | Router                          |
| ------------------------------------ | ------------------------------- |
| Connects devices within a LAN        | Routes traffic between networks |
| Usually forwards using MAC addresses | Routes using IP addresses       |
| Typically Layer 2                    | Layer 3                         |

Q17. What is the difference between LAN and WAN?

* LAN: Network within a limited area, such as an office.

* WAN: Connects networks across larger geographical areas.

Q18. What is a subnet mask?

A subnet mask identifies the network and host portions of an IPv4 address.

Example: `255.255.255.0` corresponds to `/24`.

Q19. What is a default gateway?

It is the router address a device uses to send traffic to destinations outside its local subnet.

Q20. What is TCP vs UDP?

* TCP: Connection-oriented, reliable delivery.

* UDP: Connectionless, lower overhead; delivery is not guaranteed by the protocol.

Q21. What is a VLAN?

A VLAN logically separates devices into different Layer 2 networks, even when they share physical switch infrastructure.

Q22. What is the difference between a public and private IP?

Private IP addresses are used within internal networks and are not directly routable on the public internet. Public IP addresses are globally routable.

Private IPv4 ranges:

* `10.0.0.0/8`

* `172.16.0.0/12`

* `192.168.0.0/16`

### Important ports to memorize

| Service            | Port                   |
| ------------------ | ---------------------- |
| DNS                | 53 TCP/UDP             |
| DHCP               | 67/68 UDP              |
| SSH                | 22 TCP                 |
| HTTP               | 80 TCP                 |
| HTTPS              | 443 TCP                |
| RDP                | 3389 TCP/UDP           |
| SMB / File Sharing | 445 TCP                |
| LDAP               | 389 TCP/UDP            |
| LDAPS              | 636 TCP                |
| Kerberos           | 88 TCP/UDP             |
| NTP                | 123 UDP                |
| WinRM              | 5985 HTTP / 5986 HTTPS |
| SMTP               | 25 TCP                 |
| SMTP Submission    | 587 TCP                |

## 5. Firewall and Security

Q23. What is a firewall?

A firewall controls network traffic according to security rules, such as source IP, destination IP, port, protocol, and traffic direction.

Q24. How do you troubleshoot a firewall connectivity issue?

1. Confirm the source and destination IPs.

2. Identify the required port and protocol.

3. Test connectivity using `Test-NetConnection`.

4. Review firewall rules, logs, and deny events.

5. Check routing and endpoint firewall settings.

6. Make changes only with proper authorization and approval.

7. Retest and document the result.

Example:

PowerShell

```
Test-NetConnection 192.168.1.20 -Port 445
```

Q25. What is patch management?

Patch management is the process of testing, approving, deploying, and verifying operating system and application updates to fix vulnerabilities and bugs.

Q26. How do you secure company computers?

* Apply security patches.

* Use antivirus or endpoint protection.

* Enforce least-privilege access.

* Configure firewall policies.

* Use strong authentication and MFA where available.

* Monitor security alerts and maintain backups.

## 6. Backup, RAID and Asset Management

Q27. What is a backup?

A backup is a separate copy of data used to recover files or systems after deletion, corruption, or failure.

Q28. What is the 3-2-1 backup rule?

Maintain three copies of data, on two different types of storage, with one copy off-site or isolated from the primary environment.

Q29. What is RAID?

RAID combines multiple disks for redundancy, performance, or both. RAID is not a replacement for backup.

| RAID    | Purpose                                   |
| ------- | ----------------------------------------- |
| RAID 0  | Performance; no redundancy                |
| RAID 1  | Disk mirroring                            |
| RAID 5  | Striping with single-disk fault tolerance |
| RAID 6  | Striping with two-disk fault tolerance    |
| RAID 10 | Mirroring and striping                    |

Q30. How do you manage IT assets?

Maintain records of asset tag, serial number, assigned user, configuration, warranty, software, location, and lifecycle status. Update the inventory during allocation, replacement, and return.

## 7. Windows and Linux Administration

Q31. How do you troubleshoot a slow computer?

1. Check CPU, RAM, and disk usage in Task Manager.

2. Check disk space and startup applications.

3. Review Event Viewer for errors.

4. Check updates and endpoint protection alerts.

5. Check disk health and hardware condition.

6. Apply approved fixes and confirm performance.

Q32. What is Event Viewer?

Event Viewer is a Windows tool used to review application, system, security, and other logs to diagnose issues.

Q33. What are some basic Linux commands?

| Command                    | Purpose                    |
| -------------------------- | -------------------------- |
| `pwd`                      | Show current directory     |
| `ls -l`                    | List files and permissions |
| `cd`                       | Change directory           |
| `ip addr`                  | Display IP configuration   |
| `ping`                     | Test connectivity          |
| `df -h`                    | Check disk space           |
| `free -h`                  | Check memory               |
| `systemctl status service` | Check service status       |
| `journalctl -u service`    | Review service logs        |

Q34. How would you install Windows Server?

1. Verify hardware or VM requirements.

2. Boot from the approved installation media.

3. Select the edition and complete installation.

4. Set the Administrator password.

5. Configure hostname, static IP, subnet mask, gateway, and DNS.

6. Apply approved updates.

7. Install required roles, such as AD DS, DNS, DHCP, or File Server.

8. Configure security, backup, and monitoring.

9. Test services and document the configuration.

## 8. Practical Troubleshooting Scenarios

### Q35. A user cannot access the internet. What will you do?

Answer:

"I first check the cable or Wi-Fi connection and verify the IP configuration using `ipconfig`. Then I test the default gateway, an external IP, and DNS resolution. Based on the results, I check DHCP, DNS, routing, proxy, firewall, and VPN settings before applying a fix or escalating."

cmd

```
ipconfig /all
ping 192.168.1.1
ping 8.8.8.8
nslookup google.com
```

Replace `192.168.1.1` with the actual gateway address.

### Q36. A user cannot access a shared folder. How do you troubleshoot?

1. Verify network connectivity and server availability.

2. Test access using the server's hostname and IP.

3. Confirm share and NTFS permissions.

4. Check AD group membership and user access.

5. Verify SMB connectivity on TCP 445.

6. Check name resolution and relevant firewall rules.

### Q37. A server is not responding. What will you do?

1. Confirm the scope and business impact.

2. Check monitoring alerts and server/VM status.

3. Verify network connectivity and remote access.

4. Check CPU, memory, disk, and system logs if accessible.

5. Check critical services and recent changes.

6. Follow the incident and escalation process; do not reboot a production server without authorization.

### Q38. How do you monitor a firewall daily?

"I review firewall health, CPU and memory usage, interface status, traffic and deny logs, VPN status, security alerts, and configuration changes. I investigate unusual traffic and document findings according to company policy."

### Q39. What is LAN and WAN management?

"It involves maintaining local and wide-area connectivity, monitoring switches and routers, managing IP addressing and VLANs, troubleshooting connectivity, and coordinating with network teams or ISPs when required."

### Q40. How do you handle a critical IT incident?

"I assess the impact and urgency, log and prioritize the incident, troubleshoot using a structured approach, communicate updates to affected users, and involve the appropriate teams. After service restoration, I document the root cause, resolution, and preventive actions."

## 9. HR and Experience Questions

Q41. Why do you want to become a System Administrator?

"I enjoy troubleshooting IT issues and understanding how systems, networks, and servers work together. I want to expand from end-user support into server administration, infrastructure monitoring, security, and automation."

Q42. Do you have experience managing Windows and Linux servers?

Answer honestly. If your experience is mainly through a home lab, say:

"I have hands-on experience with Windows support and Active Directory tasks. I am also building my system administration skills through a lab environment, including Windows Server, DNS, DHCP, and domain management. I am continuing to develop my Linux administration knowledge."

Q43. The job requires 3–5 years of experience. Why should we consider you?

"My current experience has given me a practical foundation in IT support, troubleshooting, user management, Microsoft 365, and networking. I have a strong learning mindset and am actively building my system administration skills. I can contribute through my existing support experience while developing into broader infrastructure responsibilities."

Only use this answer if it accurately reflects your experience.

## Final revision checklist

Interview preparation

0 of 8 revised

Active Directory, DNS, DHCP and GP0

IP addressing, subnetting, VLAN, LAN/WAN

Firewall rules, logs and port testing

Windows Server installation and roles

Backup, restore and RAID

Linux commands and service troubleshooting

Patching, antivirus and endpoint security

Real troubleshooting examples from my work

My advice: For this role, prioritize practical troubleshooting and explain what you personally have done. Don't claim production server or firewall configuration experience if you have only studied it. Your hands-on lab experience is worth discussing, provided you clearly distinguish it from your workplace experience.
