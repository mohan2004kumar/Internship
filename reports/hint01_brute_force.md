# Hint 1: Brute Force Login Attempts

## Simulation
I simulated a brute force attack on my Windows 10 VM using a PowerShell script to attempt 11 failed logins followed by a successful login.

### Command Used
```powershell
for ($i=1; $i -le 11; $i++) {
    net use \\127.0.0.1\IPC$ /user:FakeUser WrongPassword
}
net use \\127.0.0.1\IPC$ /user:FakeUser P@ssword123

Logs Generated
Windows Event Viewer:
Event ID 4625 (Failed Logon): 11 events for FakeUser with failure reason "Unknown user name or bad password".
Event ID 4624 (Successful Logon): 1 event for FakeUser after the failed attempts.
Wazuh Dashboard: Displayed the same events, confirming that Winlogbeat forwarded the logs correctly.


Screenshots
Event Viewer Logs: 
Wazuh Logs:

Analysis
Pattern Observed: 11 failed login attempts (Event ID 4625) for user FakeUser within a short time frame, followed by a successful login (Event ID 4624).
Fields Analyzed:
win.eventdata.targetUserName: FakeUser
win.eventdata.failureReason: "Unknown user name or bad password"
win.eventdata.logonType: 3 (Network logon)
Count: 11 failed logins in quick succession.
MITRE ATT&CK Mapping:
T1110: Brute Force (Credential Access tactic): The repeated failed login attempts indicate a brute force attack to guess credentials.
T1078: Valid Accounts (Persistence/Privilege Escalation tactic): The successful login after failed attempts suggests the attacker gained access using valid credentials.
