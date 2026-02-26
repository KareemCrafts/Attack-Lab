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
nmap -sS 192.168.1.9
```
Result showed port 445 (SMB) was open — this became the attack target.

<img width="1279" height="799" alt="image" src="https://github.com/user-attachments/assets/155032cc-a9d2-4fca-8ee2-b30b37462fa1" />


### 2. Setting Up Kali
Checked Kali's IP address to confirm both machines were on the same network.
```bash
ip a
```
![WhatsApp Image 2026-02-26 at 5 11 02 PM](https://github.com/user-attachments/assets/31c9a607-c5e1-4abc-bc31-9e49bd60b4a4)


### 3. Confirming Connection
Pinged the Windows machine from Kali to confirm connectivity.
```bash
ping 192.168.1.9
```

<img width="1279" height="799" alt="image" src="https://github.com/user-attachments/assets/155032cc-a9d2-4fca-8ee2-b30b37462fa1" />

---

### 4. Enabling Audit Logging on Windows
Before launching the attack I enabled full authentication logging on Windows so every attempt would be recorded.
```bash
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
```

<img width="1342" height="755" alt="image" src="https://github.com/user-attachments/assets/2233dd0b-220a-42fa-971f-0fe2f65b0e25" />


---

### 5. Brute Force Attack with Hydra
Launched a brute force attack against the SMB service targeting the administrator account.
```bash
hydra -l administrator -P passwords.txt rdp://192.168.1.9 -t 4 -V
```

<img width="1279" height="799" alt="image" src="https://github.com/user-attachments/assets/5a4b331f-d9cc-4ad6-9cf5-988da1592c97" />



---

### 6. Brute Force Attack with netexec
Used netexec as a more modern and reliable tool against Windows 11 SMB.
```bash
netexec smb 192.168.1.9 -u administrator -p passwords.txt --ignore-pw-decoding
```

<img width="1279" height="799" alt="image" src="https://github.com/user-attachments/assets/942005c6-cba6-4f8d-ac5b-7629ff21af40" />



---

### 7. Detection — Windows Event Viewer
Opened Windows Event Viewer and observed a flood of Event ID 4625 — failed logon attempts — all showing Kali's IP address as the source.

Path: Windows Logs → Security → Filter by Event ID 4625

<img width="1183" height="991" alt="image" src="https://github.com/user-attachments/assets/69093244-2c62-4bcb-bae3-1c715eb3dd09" />

### 8. Detection — Windows Event Viewer (same ip from kali)

<img width="1177" height="985" alt="image" src="https://github.com/user-attachments/assets/ebc21e1f-4078-4524-92c9-7bbd2dbb66c8" />

---

### 8. Mitigation
To prevent this attack from succeeding, I enabled an account lockout policy. After 5 failed login attempts the account locks automatically — making brute force useless.
```bash
net accounts /lockoutthreshold:5
```

In real environments this number is a balance between security and usability. Too low and you lock out legitimate users. Too high and you give attackers too much room.

---

## What I Learned
Performing this lab gave me real understanding of how brute force attacks work and how they appear in logs. Every failed attempt left a trace with the attacker's IP visible. This is exactly what SOC analysts look for when detecting attacks in real environments. Recon tells you where to attack. Logs tell you it happened.

---

## ⚠️ Disclaimer
This lab was performed on my own devices on a private network for educational purposes only.



