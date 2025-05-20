# Hint 1: Brute Force Login Attempts

## Simulation
I simulated a brute force attack on my Windows 10 VM using a PowerShell script to attempt 11 failed logins followed by a successful login.

### Command Used
```powershell
for ($i=1; $i -le 11; $i++) {
    net use \\127.0.0.1\IPC$ /user:FakeUser WrongPassword
}
net use \\127.0.0.1\IPC$ /user:FakeUser P@ssword123

