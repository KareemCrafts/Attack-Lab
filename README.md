# Home Attack Lab | Kali vs Windows 11

## Objective
Simulate a real brute force attack between two laptops and detect it using Windows security logs.

## Setup
- **Attacker:** Kali Linux running in VirtualBox
- **Target:** Windows 11 (physical machine)
- **Network:** Both devices connected on the same local WiFi

## Tools Used
- nmap
- Hydra
- netexec
- Windows Event Viewer
- auditpol

---

## Steps

### 1. Reconnaissance
First I needed to find what ports were open on the target machine.

```bash
ip a
ping 192.168.1.9
nmap -sS 192.168.1.9

![image](https://github.com/user-attachments/assets/735e6cda-61d4-44db-a90b-359a27136617)



