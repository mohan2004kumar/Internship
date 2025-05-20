# Hint 3: USB Drive Insertion

## Overview
This simulation demonstrates a USB drive insertion, mimicking an attacker using removable media to deliver malware. I performed this on my Windows 10 VM to generate logs for analysis.

## Simulation Details
I attached a virtual USB device to my Windows 10 VM, copied a dummy batch file (`dummy.bat`) from the USB, and executed it to simulate a malicious action.

### Command Used
```bat
@echo off
echo This is a simulated malicious file from USB
ping 8.8.8.8 -n 3'''

Description: The script outputs a message and pings 8.8.8.8 to simulate network activity.
Logs Generated
The simulation generated logs in both Event Viewer and the Wazuh Dashboard.

Windows System Logs (Event Viewer):
Event ID 20001/10000 (Device Setup): Logged the USB device connection.
Sysmon Logs (Event Viewer):
Event ID 11 (File Create): Logged the copying of dummy.bat.
Event ID 1 (Process Create): Logged the execution of dummy.bat.
Event ID 3 (Network Connection): Logged the ping to 8.8.8.8.
Wazuh Dashboard: Displayed the same events, confirming log forwarding.
Screenshots
Below are the screenshots capturing the logs:

System Logs:
Sysmon Logs:
Wazuh Logs:
Log Analysis
Analyzing the logs helps identify patterns indicative of malicious activity via USB.

Pattern Observed: USB device insertion, followed by file creation, process execution, and network activity.
Key Fields Analyzed:
data.win.eventdata.targetFilename: C:\Users\testuser\Downloads\dummy.bat
data.win.system.commandLine: Contains dummy.bat
data.win.eventdata.destinationIp: 8.8.8.8
MITRE ATT&CK Mapping:
T1091: Replication Through Removable Media (Initial Access): USB used to deliver a file.
T1059: Command and Scripting Interpreter (Execution): Batch file execution.
T1071: Application Layer Protocol (Command and Control): Network activity simulating C2.

