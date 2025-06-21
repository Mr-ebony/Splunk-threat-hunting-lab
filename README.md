# 🔍 Splunk Threat Hunting Lab

## 💡 Goal
This project demonstrates how to simulate, detect, and investigate cyber attacks using a SIEM tool (Splunk) in a home lab.
I used Brute-force attack (From Kali Linux) on Windowslogin to simulate failed Remote Desktop Protocol (RDP) or local login attempts, which are recorded in Windows Security logs. Next, I imported the windows Logs into Splunk were i carried out some search functions, create alert for failed login attempts, and dashboard for visualization. Finally, I carried out a detection Rules and MITRE ATT&CK Mapping. 
Note: This project should be read in combination with the My-Home-SOC-Lab which demonstrate the installation of VirtualBox, kali Linux (attacker) and Window (victime) VM. 

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
