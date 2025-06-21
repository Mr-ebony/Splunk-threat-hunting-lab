# 🔍 Splunk Threat Hunting Lab

## 💡 Goal
This project demonstrates how to simulate, detect, and investigate cyber attacks using a SIEM tool (Splunk) in a home lab.
I used a Brute-force attack (From Kali Linux) on Windows login to simulate failed Remote Desktop Protocol (RDP) or local login attempts, which are recorded in Windows Security logs. Next, I imported the Windows logs into Splunk, where I carried out some search functions, created an alert for failed login attempts, and a dashboard for visualisation. Finally, I implemented a detection rule and performed MITRE ATT&CK Mapping. 
Note: This project should be read in combination with the [My-Home-SOC-Lab](https://github.com/Mr-ebony/My-Home-SOC-Lab.git), which demonstrates the installation of VirtualBox, Kali Linux (attacker) and Windows (victim) VMs. 

## 🧰 Tools Used
- Splunk Free (local install)
- Kali Linux (attacker)
- Ubuntu Linux (victim)
- VirtualBox

## 🛠️ Lab Setup
1. Installed Splunk on Ubuntu
2. Monitored `/var/log/auth.log`
3. Attacked victim with Hydra from Kali

## 💣 Attack Simulation
- **Command**: `hydra -l root -P rockyou.txt ssh://<victim-ip>`
- **Effect**: Many failed SSH logins

## 🔍 Threat Hunting in Splunk
**Search used**:
```spl
index=* "Failed password"
