Incident Response & Detection Project – Azure SOC Lab
Overview

This project demonstrates end‑to‑end SOC, Incident Response (IR), and Threat Hunting capabilities using Microsoft Sentinel, Microsoft Defender for Endpoint (MDE), Azure Infrastructure, Honeypots, and a Brute Force attack simulation using Kali Linux.

The lab is designed to replicate real‑world enterprise security operations, including:

Log ingestion and SIEM configuration

Detection engineering & analytics rules

Endpoint Detection & Response (EDR)

Honeypot deployment

Active attack simulation

Incident investigation and response

This repository is written as a technical portfolio artifact to demonstrate hands‑on cybersecurity experience aligned with SOC Analyst, Incident Responder, and Threat Hunter roles.

Architecture Diagram
+----------------------+        +---------------------------+
| Kali Linux (Attacker)| -----> | Azure VM (Honeypot / MDE) |
|  SSH / Brute Force   |        |  Linux & Windows Targets  |
+----------------------+        +---------------------------+
                                         |
                                         v
                           +-----------------------------+
                           | Microsoft Defender for      |
                           | Endpoint (EDR Telemetry)    |
                           +-----------------------------+
                                         |
                                         v
                           +-----------------------------+
                           | Microsoft Sentinel (SIEM)   |
                           | Analytics, Hunting, IR      |
                           +-----------------------------+
Technologies Used

Microsoft Azure (VMs, Networking, Log Analytics)

Microsoft Sentinel (SIEM/SOAR)

Microsoft Defender for Endpoint (EDR/XDR)

Azure Monitor & Log Analytics

Kali Linux (Attack Simulation)

Linux & Windows Honeypots

MITRE ATT&CK Framework

Objectives

✔ Detect brute force attacks using Sentinel analytics rules
✔ Generate EDR alerts using Microsoft Defender for Endpoint
✔ Deploy and monitor a honeypot to capture attacker behavior
✔ Perform incident triage, investigation, and response
✔ Demonstrate threat hunting using KQL
✔ Showcase SOC‑level documentation and technical depth

Step 1 – Azure Environment Setup
1.1 Create Resource Group
az group create \
  --name SOC-Lab-RG \
  --location eastus
1.2 Virtual Machines
VM	OS	Purpose
Kali-Linux	Linux	Attacker
Honeypot-Linux	Ubuntu	SSH Brute Force Target
Windows-EDR	Windows 10/11	Defender for Endpoint
1.3 Network Security Group (NSG)

Allow inbound:

TCP 22 (SSH)

TCP 3389 (RDP)

⚠️ Intentionally exposed for lab purposes only

Step 2 – Install Microsoft Defender for Endpoint
2.1 Onboard Windows VM to MDE

From Microsoft 365 Defender Portal:

Settings → Endpoints → Onboarding

Download onboarding package

WindowsDefenderATPOnboardingScript.cmd

Verify:

sc query sense

📸 Screenshot: Defender portal showing onboarded device

Step 3 – Deploy Honeypot (Linux SSH)
3.1 Install Cowrie Honeypot
sudo apt update
sudo apt install git python3-venv -y


git clone https://github.com/cowrie/cowrie.git
cd cowrie
python3 -m venv cowrie-env
source cowrie-env/bin/activate
pip install -r requirements.txt
3.2 Configure SSH Port Redirection
sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222

Start Cowrie:

bin/cowrie start

📸 Screenshot: Cowrie capturing SSH login attempts

Step 4 – Kali Linux Brute Force Attack
4.1 Install Hydra
sudo apt update && sudo apt install hydra -y
4.2 Create Credential List
echo -e "root\nadmin\nuser" > users.txt
echo -e "password\n123456\nadmin" > passwords.txt
4.3 Execute Brute Force Attack
hydra -L users.txt -P passwords.txt ssh://<TARGET_PUBLIC_IP> -t 4 -V

📸 Screenshot: Kali Linux brute force output

Step 5 – Log Ingestion into Microsoft Sentinel
5.1 Create Log Analytics Workspace
az monitor log-analytics workspace create \
  --resource-group SOC-Lab-RG \
  --workspace-name SOC-Workspace
5.2 Enable Sentinel

Azure Portal → Microsoft Sentinel → Add Workspace

📸 Screenshot: Sentinel enabled workspace

Step 6 – Detection & Analytics Rules
6.1 Brute Force Detection (KQL)
SecurityEvent
| where EventLevelName == "Failure"
| where EventID == 4625
| summarize FailedAttempts = count() by Account, IpAddress, bin(TimeGenerated, 5m)
| where FailedAttempts > 5
6.2 Create Analytics Rule

Schedule: Every 5 minutes

Severity: High

MITRE: T1110 – Brute Force

📸 Screenshot: Sentinel Analytics Rule

Step 7 – Defender for Endpoint Alerting

MDE automatically generates:

Suspicious authentication activity

SSH brute force behavior

Lateral movement indicators

📸 Screenshot: Defender alert timeline

Step 8 – Incident Investigation
8.1 Sentinel Incident View

Correlated events

IP address reputation

Affected assets

8.2 Investigation Queries
SigninLogs
| where ResultType != 0
| project TimeGenerated, IPAddress, UserPrincipalName
DeviceNetworkEvents
| where RemotePort == 22
| summarize count() by RemoteIP

📸 Screenshot: Incident investigation graph

Step 9 – Incident Response Actions

Isolate compromised endpoint (MDE)

Block attacker IP (NSG / Firewall)

Reset credentials

Document incident timeline

Invoke-DeviceIsolation
Step 10 – Threat Hunting
10.1 MITRE ATT&CK Mapping
Technique	Description
T1110	Brute Force
T1021	Remote Services
10.2 Hunting Query
DeviceLogonEvents
| where ActionType == "LogonFailed"
| summarize Attempts=count() by DeviceName, RemoteIP
| where Attempts > 10

📸 Screenshot: Threat hunting results

Key Skills Demonstrated

✅ SOC Monitoring & Alerting
✅ Incident Response Lifecycle
✅ Detection Engineering (KQL)
✅ Endpoint Security (EDR/XDR)
✅ Cloud Security (Azure)
✅ Threat Hunting & MITRE ATT&CK

Conclusion

This project simulates a realistic enterprise security incident from attack execution to detection, investigation, and response. It showcases hands‑on cybersecurity expertise aligned with professional SOC and Incident Response roles.

Author

[Monica Defensiani]
Cybersecurity | SOC Analyst | Incident Response | Threat Hunting

⭐ If you find this project useful, feel free to star the repository!

I’ve built a full, GitHub-ready README portfolio project for you in the canvas that’s technical, convincing, and SOC/IR-focused. This is written exactly the way hiring managers, SOC leads, and blue-team interviewers expect to see real lab experience documented.

Why this works well for a cybersecurity portfolio

This project clearly shows that you can:

Design a realistic Azure SOC lab

Deploy and integrate Microsoft Sentinel + Defender for Endpoint

Use honeypots intentionally (not just theory)

Execute and detect a real brute-force attack from Kali

Write KQL detections, hunting queries, and analytics rules

Follow the Incident Response lifecycle

Map activity to MITRE ATT&CK

Communicate findings like a working SOC analyst, not a student

This is not a beginner lab write-up — it reads like someone who’s done SOC work.

How to use this in GitHub (important)

Create a new GitHub repo
Name idea:
azure-soc-incident-response-sentinel-mde

Copy everything from the canvas into:

README.md


Replace:

<TARGET_PUBLIC_IP>

[Monica Defensiani]

Add your screenshots where the 📸 markers are:

Azure VMs

Sentinel analytics rules

Defender alerts

Kali brute force output

Cowrie logs

Incident investigation graphs
