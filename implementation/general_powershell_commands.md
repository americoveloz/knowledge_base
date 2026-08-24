# Powershell commands

# Setting Windows Updates to automatic

## Stop windows update service
net stop wuauserv

## Set automatic updates to 4 (enabled)
cscript scregedit.wsf /AU 4

## Start windows update service
net start wuauserv

## Downloading and installing updates
wuauclt /detectnow

#######################################################################################

# Adding roles and features

## List or search Windows Server roles 

Get-WindowsFeature *file* # to search Fileserver role

## Install Windows Server role

Install-WindowsFeature -Name AD-Domain-Services, DNS -IncludeManagementTools

#######################################################################################

# Enable or disable HYPER-V
bcdedit /set hypervisorlaunchtype [on|off]
reboot

#######################################################################################

# Enable Remote Administration

## Enabling remote administration. Enabled by default on Windows Server
Configure-SMRemoting.exe -Enable 

## Enable remote administration with PowerShell
Enable-PSRemoting -force

## Enable remote administration with winrm
Winrm quickconfig


#######################################################################################

# Configure Windows Firewall

## Enable or disable Firewall

Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

## Filter Firewall rules
Get-NetFirewallRule *icmp* | Format-table

## Enable or disable firewall rule
Set-NetFirewallRule -Name "CoreNet-Diag-ICMP4-EchoRequest-In" -Enabled False

## Create new firewall rule
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow


#######################################################################################

# GPO provisioning command after IPAM feature has been installed on server

Invoke-IpamGpoProvisioning -Domain "domain.com" -GpoPrefixName "IPAM1" -IpamServerFqdn "ipamserver.domain.com"

Note: after the previous command, execute gpupdate /force on the IPAM server.
