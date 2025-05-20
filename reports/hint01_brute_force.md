# Hint 1: Brute Force Login Attempts

## Overview
This simulation demonstrates a brute force attack, where an attacker attempts to gain unauthorized access by repeatedly trying different credentials. I performed this on my Windows 10 VM to generate logs for analysis.

## Simulation Details
I used a PowerShell script to simulate a brute force attack by attempting 11 failed logins followed by a successful login. This mimics an attacker trying to guess credentials to access the system.

### Command Used
```powershell
for ($i=1; $i -le 11; $i++) {
    net use \\127.0.0.1\IPC$ /user:FakeUser WrongPassword
}
net use \\127.0.0.1\IPC$ /user:FakeUser P@ssword123
Description: The script attempts 11 failed logins with the username FakeUser and an incorrect password (WrongPassword), followed by a successful login with the correct password (P@ssword123). The net use command targets the local machine (127.0.0.1) via the IPC$ share, simulating a network logon attempt.
Logs Generated
The simulation generated logs in both Windows Event Viewer and the Wazuh Dashboard, confirming that the activity was captured and forwarded correctly.

Windows Event Viewer:
Event ID 4625 (Failed Logon): Logged 11 events for FakeUser with the failure reason "Unknown user name or bad password".
Event ID 4624 (Successful Logon): Logged 1 event for FakeUser after the failed attempts, indicating a successful login.
Wazuh Dashboard: Displayed the same events, confirming that Winlogbeat successfully forwarded the logs to Wazuh for indexing.
Screenshots
Below are the screenshots capturing the logs from both Event Viewer and Wazuh Dashboard:

Event Viewer Logs:
Wazuh Logs:
Log Analysis
Analyzing the logs helps identify patterns indicative of a brute force attack, which can be used to develop detection rules.

Pattern Observed: 11 failed login attempts (Event ID 4625) for the user FakeUser within a short time frame (e.g., within a minute), followed by a successful login (Event ID 4624).
Key Fields Analyzed:
win.eventdata.targetUserName: FakeUser
win.eventdata.failureReason: "Unknown user name or bad password"
win.eventdata.logonType: 3 (Network logon)
Count: 11 failed logins in quick succession
MITRE ATT&CK Mapping:
T1110: Brute Force (Credential Access tactic): The repeated failed login attempts indicate a brute force attack to guess credentials.
T1078: Valid Accounts (Persistence/Privilege Escalation tactic): The successful login after failed attempts suggests the attacker gained access using valid credentials.
