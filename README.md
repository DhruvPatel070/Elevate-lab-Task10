# Elevate-lab-Task10


# Firewall Concepts and Principles

A firewall is a security system that monitors and controls incoming and outgoing network traffic based on predefined rules.

In Windows operating systems, firewall functionality is provided by **Windows Defender Firewall with Advanced Security (WFAS)**.

WFAS is a **stateful, host-based firewall**, meaning it tracks the state of network connections and applies rules accordingly.

---

## Core Concepts

| Concept | Description |
|------|------------|
| **Stateful Filtering** | WFAS tracks active connections. If an outbound connection is allowed, the related inbound response traffic is automatically permitted. No additional inbound rule is required. |
| **Network Profiles** | Windows Firewall supports **Domain**, **Private**, and **Public** profiles. Each profile can have different firewall rules depending on the network environment. |
| **Inbound / Outbound Rules** | Inbound rules control traffic entering the system. Outbound rules control traffic leaving the system. By default, outbound traffic is allowed and inbound traffic is blocked unless explicitly permitted. |

---
# Firewall Configuration and Rules

The firewall is configured to allow only required inbound services while keeping all other inbound traffic blocked by default. This approach ensures a secure default-deny posture while permitting necessary network access.

---

## Configuration Using PowerShell

| Action | PowerShell Command | Purpose |
|------|------------------|--------|
| Allow SSH (Port 22) | `New-NetFirewallRule -DisplayName "Allow SSH Inbound" -Direction Inbound -LocalPort 22 -Protocol TCP -Action Allow` | Enables secure remote administration |
| Allow HTTP (Port 80) | `New-NetFirewallRule -DisplayName "Allow HTTP Inbound" -Direction Inbound -LocalPort 80 -Protocol TCP -Action Allow` | Allows inbound web traffic |
| Deny Other Inbound Traffic | *(Default policy)* | Blocks all unsolicited inbound connections |

---

## Configuration Using netsh advfirewall

```bash
# Allow SSH (Port 22)
netsh advfirewall firewall add rule name="Allow SSH Inbound" dir=in action=allow protocol=TCP localport=22

# Allow HTTP (Port 80)
netsh advfirewall firewall add rule name="Allow HTTP Inbound" dir=in action=allow protocol=TCP localport=80

```
# Final Ruleset Summary

The table below summarizes the final firewall rules configured on the system.  
Only required inbound services are allowed, while all other unsolicited inbound traffic is blocked by default.

---

## Firewall Rules Overview

| Rule Name | Direction | Protocol | Local Port | Action | Purpose |
|---------|-----------|----------|------------|--------|---------|
| Allow SSH Inbound | Inbound | TCP | 22 | Allow | Remote Management |
| Allow HTTP Inbound | Inbound | TCP | 80 | Allow | Web Server Access |
| Default Inbound Policy | Inbound | All | All | Block | Deny all other unsolicited traffic |

---

# Testing, Logging, and IP Blocking

This section describes how firewall rules are tested, how firewall logging is enabled and reviewed, and how malicious IP addresses are handled.

---

## Test Connectivity

Connectivity testing is performed to verify that allowed ports are accessible and blocked ports are denied as expected.

| Test Scenario | Tool | Expected Result |
|-------------|------|----------------|
| Allowed Port (80) | Test-NetConnection -Port 80 or telnet localhost 80 | Connection succeeds or is refused if no service is running, but does not time out |
| Denied Port (8080) | Test-NetConnection -Port 8080 or telnet localhost 8080 | Connection fails or times out |

---

## Enable and Observe Firewall Logs

Firewall logging is disabled by default and must be enabled manually to monitor allowed and blocked network traffic.

### PowerShell Command to Enable Logging

Set-NetFirewallProfile  
-Profile Public  
-LogFileName "%systemroot%\system32\LogFiles\Firewall\pfirewall.log"  
-LogBlocked True  
-LogSuccessful True  

---

## Log Details

- **Log file location:**  
  `C:\Windows\System32\LogFiles\Firewall\pfirewall.log`

- **Blocked traffic entries:** `DROP`

- **Allowed traffic entries:** `ACCEPT`

---

# Block a Malicious IP Address

To block a known malicious IP address (example: **192.0.2.1**), a specific inbound firewall rule is created with the action set to block. This ensures that all traffic originating from the malicious source is denied.

### Block Rule Configuration

New-NetFirewallRule  
-DisplayName "Block Malicious IP 192.0.2.1"  
-Direction Inbound  
-Action Block  
-RemoteAddress 192.0.2.1  

**Note:**  
Windows Defender Firewall with Advanced Security (WFAS) prioritizes **block rules** over **allow rules**, ensuring that malicious traffic is dropped immediately.

---

# Documentation and Impact Explanation

## Rule Documentation

This document provides complete firewall documentation, including:

- Firewall concepts and principles  
- PowerShell and netsh configuration commands  
- Connectivity testing procedures  
- Firewall logging configuration  
- Malicious IP blocking techniques  

---
# Impact Analysis

The table below explains the impact of the implemented firewall configuration on system security, operations, and management.

---

## Firewall Configuration Impact

| Impact Area | Description |
|------------|-------------|
| **Security Posture** | A strong default-deny inbound policy significantly reduces the attack surface. Only SSH and HTTP ports are exposed. |
| **Operational Efficiency** | Stateful filtering allows normal outbound activity without disruption while enforcing strict inbound control. |
| **Threat Intelligence** | Firewall logs help detect scanning attempts and suspicious traffic patterns for proactive defense. |
| **System Management** | PowerShell-based rule management enables quick response to new security threats and changing operational needs. |

---






