# pfSense DoS Defense Lab

## Overview
Built a virtualized network security lab using VirtualBox to simulate a real-world DoS attack and demonstrate firewall-based mitigation using pfSense. The lab replicates a two-zone network architecture with an external attacker (Kali Linux) on the WAN side and a victim host (Ubuntu) on an isolated internal LAN segment protected by pfSense.

## Tools & Technologies
- pfSense CE 2.7.2
- Kali Linux (Attacker)
- Ubuntu Desktop (Victim)
- Oracle VirtualBox
- Wireshark
- hping3

## Network Topology
| Device | Network Adapter | IP Address | Role |
|---|---|---|---|
| pfSense WAN | Bridged | 192.168.4.155 | Edge Firewall |
| pfSense LAN | Internal (LabNet) | 192.168.1.1 | Default Gateway |
| Kali Linux | Bridged | 192.168.4.157 | Attacker |
| Ubuntu Desktop | Internal (LabNet) | 192.168.1.100 | Victim |

## Network Diagram

```
> <img width="691" height="482" alt="image" src="https://github.com/user-attachments/assets/ff01ca30-60bb-4e28-937c-f874d03a009e" />

```

## What I Did

### 1. Built the Lab Environment
Configured three VMs in VirtualBox with separate network segments — a bridged WAN segment connected to the home network and an isolated internal LAN (LabNet) behind pfSense.

### 2. Configured pfSense
Set up pfSense as the edge firewall, configured DHCP on the LAN interface to serve the internal subnet, and created firewall rules to control traffic between WAN and LAN zones.

### 3. Simulated a DoS Attack
Used hping3 on Kali Linux to launch a TCP SYN flood against the Ubuntu victim machine, routing traffic through pfSense's WAN interface into the internal LAN segment.

```bash
sudo hping3 -S -p 80 --flood 192.168.1.100
```

### 4. Captured Attack Traffic
Monitored the flood in real time using Wireshark on Ubuntu, confirming packets were arriving from the attacker.

![Wireshark SYN Flood](screenshots/wireshark_flood.png)

### 5. Mitigated the Attack
Created a block rule in pfSense targeting Kali's IP with logging enabled. Applied the rule and confirmed in Wireshark that the flood immediately stopped reaching Ubuntu.

![pfSense Block Rule](screenshots/block_rule.png)

### 6. Verified in pfSense Logs
Checked firewall logs under Status → System Logs → Firewall and confirmed hundreds of blocked SYN packets logged against the Block Kali DoS rule.

![pfSense Firewall Logs](screenshots/firewall_logs.png)

## Screenshots

| Screenshot | Description |
|---|---|
| ![VMs](screenshots/virtualbox_vms.png) | VirtualBox showing all 3 VMs |
| ![Console](screenshots/pfsense_console.png) | pfSense console with WAN/LAN IPs |
| ![Rules](screenshots/firewall_rules.png) | pfSense WAN firewall rules |
| ![Flood](screenshots/wireshark_flood.png) | Wireshark capturing SYN flood |
| ![Blocked](screenshots/wireshark_blocked.png) | Wireshark after block rule applied |
| ![Logs](screenshots/firewall_logs.png) | pfSense logs showing blocked traffic |

## Key Takeaways
- Configured a pfSense firewall with multi-zone network segmentation (WAN/LAN)
- Demonstrated how firewall rules control traffic flow between network segments
- Simulated a real DoS attack and mitigated it using firewall-based packet filtering
- Used Wireshark to capture and analyze attack traffic in real time
- Understood how NAT enables private LAN hosts to reach the internet through a firewall
