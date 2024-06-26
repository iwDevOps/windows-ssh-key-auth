# Enable SSH Key authentication on Windows server
SSH key authentication for Windows 

## Install OpenSSH for Windows 
```powershell
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'
```

## Install Components
```powershell
# Install the OpenSSH Client
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

# Install the OpenSSH Server
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

## Start the sshd service
```powershell
Start-Service sshd
```

## OPTIONAL but recommended:
```powershell
Set-Service -Name sshd -StartupType 'Automatic'
```
## Confirm the Firewall rule is configured. It should be created automatically by setup. Run the following to verify
```powershell
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
} else {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
}
```
## Connect 
```powershell
ssh domain\username@servername
```
## SSH Keys
```powershell
# Set the sshd service to be started automatically
Get-Service -Name sshd | Set-Service -StartupType Automatic

# Now start the sshd service
Start-Service sshd
```
## Generate keys
```powershell
ssh-keygen -t ed25519
```

## Enable ssh-agent
```powershell
# By default the ssh-agent service is disabled. Configure it to start automatically.
# Make sure you're running as an Administrator.
Get-Service ssh-agent | Set-Service -StartupType Automatic

# Start the service
Start-Service ssh-agent

# This should return a status of Running
Get-Service ssh-agent

## Administrator keys go in administrators_authorized_keys, run Powershell as administrator
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQAB_keymySSHadmin" >> C:\ProgramData\ssh\administrators_authorized_keys
```

## Update Powershell with winget
```powershell
# Search 
PS >  winget.exe search Microsoft.Powershell

Name               Id                           Version   Source
-----------------------------------------------------------------
PowerShell         Microsoft.PowerShell         7.3.9.0   winget
PowerShell Preview Microsoft.PowerShell.Preview 7.4.0.101 winget

# Install
PS > winget install --id Microsoft.Powershell --source winget

Downloading https://github.com/PowerShell/PowerShell/releases/download/v7.3.9/PowerShell-7.3.9-win-x64.msi
  ██████████████████████████████   100 MB /  100 MB
Successfully verified installer hash
Starting package install...
**Successfully installed**

