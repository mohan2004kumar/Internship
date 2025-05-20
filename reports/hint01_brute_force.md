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

