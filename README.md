# pfSense DoS Defense Lab

## Overview
Built a virtualized network security lab using VirtualBox to simulate a real-world DoS attack and demonstrate firewall-based mitigation using pfSense. This lab demonstrates a two-zone network architecture with an external attacker (Kali Linux) on the WAN side and a victim host (Ubuntu) on an isolated internal LAN segment protected by pfSense.

## Network Diagram

> <img width="691" height="482" alt="Firewall Defense Lab drawio" src="https://github.com/user-attachments/assets/2ebd449e-0e09-4340-aeb1-76b3827596de" />

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
| pfSense WAN | External | 192.168.4.155 | Edge Firewall |
| pfSense LAN | Internal | 192.168.1.1 | Default Gateway |
| Kali Linux | External | 192.168.4.157 | Attacker |
| Ubuntu Desktop | Internal | 192.168.1.100 | Victim |

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
> <img width="1326" height="759" alt="image" src="https://github.com/user-attachments/assets/bffda2de-8610-4a55-bd84-c1044e18c030" />


### 4. Captured Attack Traffic
Monitored the flood in real time using Wireshark on Ubuntu, confirming packets were arriving from the attacker.

> <img width="1278" height="795" alt="image" src="https://github.com/user-attachments/assets/d9839089-fd7a-43e7-a1dc-e0f87a40c4b4" />

### 5. Mitigated the Attack
Created a block rule in pfSense targeting Kali's IP with logging enabled. Applied the rule and confirmed in Wireshark that the flood immediately stopped reaching Ubuntu.

> <img width="1705" height="140" alt="image" src="https://github.com/user-attachments/assets/fe716613-6bac-4e70-8cd9-dedeb4996435" />
> <img width="1276" height="796" alt="image" src="https://github.com/user-attachments/assets/5566dd4f-6150-4e4b-ab35-b667553603eb" />

### 6. Verified in pfSense Logs
Checked firewall logs under Status → System Logs → Firewall and confirmed hundreds of blocked SYN packets logged against the Block Kali DoS rule.

> <img width="1771" height="1957" alt="image" src="https://github.com/user-attachments/assets/6fec3e75-29de-409a-bba4-dbeec7e8958a" />

## Screenshots

| Screenshot | Description |
|---|---|
| <img width="1915" height="934" alt="image" src="https://github.com/user-attachments/assets/112847fa-0f06-4817-a47a-f0e9b9fe0fc5" /> | Virtual Machines Used |
| <img width="720" height="400" alt="image" src="https://github.com/user-attachments/assets/a7c92bac-f905-47ef-a159-8341c73b4b86" /> | pfSense console showing WAN and LAN interfaces |
| <img width="1808" height="1904" alt="image" src="https://github.com/user-attachments/assets/722d0c7c-448a-4309-a9a9-3c28ae5b51b1" /> | DHCP & DNS Server configured on the LAN interface |
| <img width="1757" height="838" alt="image" src="https://github.com/user-attachments/assets/0c9a265b-5f60-49fb-97fe-414b3761fcc6" /> | pfSense Firewall Rules |
| <img width="1278" height="795" alt="image" src="https://github.com/user-attachments/assets/d9839089-fd7a-43e7-a1dc-e0f87a40c4b4" /> | Wireshark capturing SYN flood | 
| <img width="1276" height="796" alt="image" src="https://github.com/user-attachments/assets/5566dd4f-6150-4e4b-ab35-b667553603eb" /> | Wireshark after block rule applied |
| <img width="1771" height="1957" alt="image" src="https://github.com/user-attachments/assets/6fec3e75-29de-409a-bba4-dbeec7e8958a" />| pfSense logs showing blocked traffic |


## Key Takeaways
- Built a two-zone network (WAN/LAN) in VirtualBox simulating a real enterprise firewall topology
- Configured pfSense with DHCP, DNS, interface assignments, and firewall rules between network zones
- Simulated a TCP SYN flood attack using hping3 routed through pfSense into an isolated internal LAN
- Captured and analyzed live attack traffic in Wireshark at the packet level in real time
- Mitigated the DoS attack with a pfSense block rule — verified via Wireshark and firewall logs
- Learned how pfSense acts as a traffic chokepoint, intercepting packets before they reach their destination
- Configured static routes on Kali to enable cross-subnet reachability between WAN and LAN
- Understood how firewall rule ordering (top-down) directly impacts traffic flow and security
