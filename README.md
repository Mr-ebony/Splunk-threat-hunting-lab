# 🔍 Splunk Threat Hunting Lab

## 💡 Goal
This project demonstrates how to simulate, detect, and investigate cyber attacks using a SIEM tool (Splunk) in a home lab.
I used a Brute-force attack (From Kali Linux) on Windows login to simulate failed Remote Desktop Protocol (RDP) or local login attempts, which are recorded in Windows Security logs. Next, I imported the Windows logs into Splunk, where I carried out some search functions, created an alert for failed login attempts, and a dashboard for visualisation. Finally, I implemented a detection rule and performed MITRE ATT&CK Mapping. 
Note: This project should be read in combination with the [My-Home-SOC-Lab](https://github.com/Mr-ebony/My-Home-SOC-Lab.git), which demonstrates the installation of VirtualBox, Kali Linux (attacker) and Windows (victim) VMs. 

## 🧰 Tools Used
- Splunk Free (local install)
- Kali Linux (attacker)
- Windows (victim)
- VirtualBox

## 🛠️ Lab Setup
1. Installed Splunk on Windows (Please see **image 1**)
    + Download **Splunk Enterprise (Free)** for Windows
    + Install it: 
      + double-click the `.msi` installer
2. Import 'WinEventLog:Security' into Splunk (Please see **image 2**)
   + To Add Logs:
     + In Splunk > Go to **Settings** > **Add Data**
     + Choose **Monitor File/Directory**
     + Select **Local Event Logs**
     + From the WinEventLog, Select **Security**
     + Set the source name to something like `windows_security_log`
     + Save and let Splunk index the logs

## 💣 Attack Simulation
1. Attacked the victim with Hydra from Kali (Please see **image 3**)
- **Command**: `hydra -l Administrator -P ~/test.txt rdp://<victim-windows-ip>`
    + `Administrator` with any valid or invalid Windows username
    + `<victim-windows-ip>` with your Windows VM IP address
    + You can check your remote desktop (rdp) port 3389 is open (please see **Port 3389 scan**). If it isn't open, could you turn on the remote desktop?
- **Effect**: Windows logs failed login attempts **(Event ID 4625)** in the Security Event Log.

**Note:** The initial idea was to use a password from **/usr/share/wordlists/rockyou**, but the number of passwords will be too much and time-consuming. I generated a random password (**test.txt**) (please see **image 4**), which was then used in the Brute Force Attack.

## 🔍 Threat Hunting in Splunk || Searching in Splunk (Windows Failed Logins)
**Search used** (Please see **Image 5**):
```spl
source="WinEventLog:Security" EventCode=4625  
```

## 🔔 Create An Alert in Splunk for Failed Logins
- Go to **Splunk Web** (Please see **Image 6**)
- **Click** `Search & Reporting` app
- **Query**: `source="WinEventLog:Security" EventCode=4625`
- Click **Save As** → Alert
- Fill in the alert settings:
  + **Title:** Failed Windows Logins Alert
  + **Description:** Alert for Event ID 4625 - failed login attempts
  + **Alert Type:** Scheduled
  + **Runs Every**: 1 hour
  + **Triggers Alert When**: Number of results > 0
- Under **Actions**, Choose:
  + [✓] Add to Triggered Alerts
  + (Optional) Email or script actions (requires setup)
- Click **Save**
##### 📌 This alert will now check every 1 hour and trigger if any failed logins are found.

## 📈 Dashboard Panels
1. **Failed Logins by Username** (Please see **Image 7, 8, 9**)  
   ##### Shows which usernames were targeted most.
+ Go to **Search & Reporting** again
+ Run this query to get failed login attempts per username:
```spl
  source="WinEventLog:Security" EventCode=4625
  | stats count by host
```
+ Click **Visualisation tab**, choose `Bar Chart`
+ Click **Save As → Dashboard Panel**
    + **Dashboard Title:** `Windows Threat Hunting`
    + **Panel Title:** `Failed Logins by Username`
    + Click **Save**
2. **Failed Logins Over Time** (Please see **Image 10, 11, 12**) 
   ##### Visual trend of login attempts
+ New Search:
```spl
source="WinEventLog:Security" EventCode=4625
| timechart count by host
```
+ Visualize as `Line Chart`
+ Save as:
  + Panel Title: `Failed Logins Over Time`
3. **Top Attacking IPs**  
   ##### Source IPs making failed login attempts.
+ New Search:
```spl
source="WinEventLog:Security" EventCode=4625
| stats count by host
```
(If `Client_Address` isn't showing, use `src` or `Workstation_Name`)
+ Visualize as `Pie Chart` or `Bar Chart`
+ Save as:
  + **Panel Title:** `Top Attacking IPs`

## 🛡 Detection Rules & MITRE ATT&CK Mapping

### 🔍 What This Project Does
- Detects brute-force login attempts on a Windows machine.
  #### Select a Technique from MITRE ATT&CK
  I used the techniques tied to brute-force login:
  + **Tactic**: Credential Access
  + **Technique**: [Brute Force - T1110](https://attack.mitre.org/techniques/T1110/)
- Maps findings to MITRE ATT&CK techniques.

**You already simulated this with Hydra. Now you’ll map your Splunk query to this technique.**

  #### 📏 Detection Logic
```spl
source="WinEventLog:Security" EventCode=4625
| stats count by Account_Name, ComputerName
| where count > 5
 ```
  **The above will flag accounts with more than 5 failed logins.**
  
  ➡️ Now save it as a detection rule:
  + Go to Search & Reporting
  + Run the query above
  + Click Save As → Alert
  + Name it: `Brute Force Detection - T1110`
  + Set to run every 5 minutes
  + Set Trigger Condition: if number of results > 0

  **Simple table describing the MITRE ATT&CK Mapping**
    
| Tactic             | Technique           | Technique ID | Detection Logic                              |
|--------------------|---------------------|--------------|-----------------------------------------------|
| Credential Access | Brute Force         | T1110        | > 5 failed login attempts for same username   |
| Initial Access    | Valid Accounts      | T1078        | Successful login after multiple failures      |

## 🧠 Key Learnings
- Brute-force attack with Hydra
- Real-time alerting with Splunk
- Building threat dashboards
- Visual correlation of attacker behaviour
- MITRE ATT&CK

## 📸 Screenshots
See the `/screenshots/` folder for visuals of alert setup and dashboard panels.
