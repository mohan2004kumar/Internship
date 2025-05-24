# Hint 7: Lateral Movement Simulation

## Simulation
I simulated a lateral movement attack on my Windows 10 VM to mimic an attacker attempting to access another system in the network after gaining initial access. I created a batch file (`lateral_sim.bat`) that attempts to connect to a remote system at `192.168.1.200` using fake credentials (`FakeAdmin/P@ssw0rd123`), tries to access a remote share, and performs a network scan using `ping`. My VM’s IP address is `192.168.136.130`, and the target IP `192.168.1.200` is on a different subnet, simulating a failed attempt to reach an unreachable system.

## Command Used
```bat
@echo off
echo Simulating lateral movement...
REM Attempt to connect to a remote share or system using fake credentials
net use \\192.168.1.200\IPC$ /user:FakeAdmin P@ssw0rd123
REM Simulate accessing a remote share
dir \\192.168.1.200\IPC$
REM Simulate a network scan or further enumeration
ping 192.168.1.200 -n 3
echo Lateral movement simulation complete.
```

## Logs Generated
- **Sysmon Logs (Event Viewer):** Captured the process creation for `lateral_sim.bat` (Event ID 1), `net.exe` command (Event ID 1), `dir` command (Event ID 1), and network connection attempt to `192.168.1.200` (Event ID 3).  
- **Wazuh Dashboard:** Displayed the Sysmon events, confirming the activity was logged and forwarded.

## Screenshots
- **Sysmon Log for `lateral_sim.bat` Execution (Event ID 1):** ![Lateral Sim Log](../screenshots/hint-7-1)  
- **Sysmon Log for `net.exe` Command (Event ID 1):** ![Net.exe Log](../screenshots/hint-7-2)  
- **Sysmon Log for Network Connection to `192.168.1.200` (Event ID 3):** ![Network Connection Log](../screenshots/hint-7-3)  
- **Wazuh Log for `net.exe` Command (Event ID 1):** ![Wazuh Net.exe Log](../screenshots/hint-7-4)  
- **Wazuh Log for Network Connection to `192.168.1.200` (Event ID 3):** ![Wazuh Network Connection Log](../screenshots/hint-7-5)

## Analysis
- **Pattern Observed:** The script execution began with `lateral_sim.bat` running (Event ID 1, see hint-7-1), which invoked `net.exe` to attempt a connection to `\\192.168.1.200\IPC$` (Event ID 1, see hint-7-2 and hint-7-4). The connection failed with "System error 67: The network name cannot be found," as the target IP `192.168.1.200` is not reachable from my VM’s subnet (`192.168.136.0/24`). The `dir` command also failed ("The network path was not found"). A `ping` to `192.168.1.200` resulted in "Destination host unreachable" replies from `192.168.1.209`, logged as a network connection attempt (Event ID 3, see hint-7-3 and hint-7-5).  
- **Fields Analyzed:**  
  - `data.win.system.commandLine`: Contains `lateral_sim.bat` (hint-7-1)  
  - `data.win.system.commandLine`: Contains `net use \\192.168.1.200\IPC$` (hint-7-2 and hint-7-4)  
  - `data.win.eventdata.sourceIp`: `192.168.136.130` (my VM’s IP)  
  - `data.win.eventdata.destinationIp`: `192.168.1.200` (target IP, hint-7-3 and hint-7-5)  
- **Impact:** The failed attempts to connect and access the remote share indicate a lateral movement attempt, while the `ping` suggests network enumeration. These activities are typical of an attacker probing the network for additional targets after gaining initial access.

## MITRE ATT&CK Mapping
- **T1059: Command and Scripting Interpreter** (Execution): The batch file (`lateral_sim.bat`) was executed via `cmd.exe` to run commands (hint-7-1).  
- **T1021: Remote Services** (Lateral Movement): The `net use` command attempted to authenticate to a remote system (`\\192.168.1.200\IPC$`) using stolen credentials, simulating lateral movement (hint-7-2 and hint-7-4).  
- **T1046: Network Service Discovery** (Discovery): The `ping` command was used to probe the network for the target system (`192.168.1.200`), indicating network enumeration (hint-7-3 and hint-7-5).

