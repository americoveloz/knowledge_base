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

#######################################################################################3

# Adding roles and features

## List or search Windows Server roles 

Get-WindowsFeature *file* # to search Fileserver role

## Install Windows Server role

Install-WindowsFeature -Name AD-Domain-Services, DNS -IncludeManagementTools

#######################################################################################3

# Enable or disable HYPER-V
bcdedit /set hypervisorlaunchtype [on|off]
reboot
