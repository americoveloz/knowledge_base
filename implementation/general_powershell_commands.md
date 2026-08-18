# Powershell commands

# Setting updates to automatic

## Stop windows update service
net stop wuauserv

## Set automatic updates to 4 (enabled)
cscript scregedit.wsf /AU 4

## Start windows updated service
net start wuauserv

## Downloading and installing updates
wuauclt /detectnow


# Adding roles and features

## List or search Windows Server roles 

Get-WindowsFeature *file* # to search Fileserver role

## Install Windows Server role

Install-WindowsFeature -Name AD-Domain-Services, DNS -IncludeManagementTools
