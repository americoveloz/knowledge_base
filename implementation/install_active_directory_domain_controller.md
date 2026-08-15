# Install Active Directory role and promote it to a Domain Controller

# Prior steps

## Renaming the server

Rename-Computer -NewName "DC01" -Restart

## Configuring a Static IP Address

Get-NetAdapter

New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress "172.16.0.10" -PrefixLength 24 -DefaultGateway "172.16.0.1"

Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "172.16.0.11","127.0.0.1"



# Install AD DS Role
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools


# Install DNS Role
Install-WindowsFeature -Name DNS -IncludeManagementTools



#### After installing the roles, the next step is to promote the server (DC01) to become the first domain controller in a new forest (Test.com).

#### Use the Install-ADDSForest cmdlet. You’ll need to provide the domain name and a password for the Directory Services Restore Mode (DSRM). Choose a strong, unique password for DSRM and store it securely.


# Define a secure string for the DSRM password

$DSRMPassword = ConvertTo-SecureString "YourStr0ngDSRMP@sswOrd!" -AsPlainText -Force

# Install the new Forest

Install-ADDSForest -DomainName "Test.com" -DomainNetbiosName "TEST" -SafeModeAdministratorPassword $DSRMPassword -InstallDNS -Force

# Alternative commnads to create new Active Directory Forest
Install-ADDSForest `
  -DomainName "corp.example.com" `
  -DomainNetbiosName "CORP" `
  -CreateDnsDelegation:$false `
  -DatabasePath "C:\Windows\NTDS" `
  -LogPath "C:\Windows\NTDS" `
  -SysvolPath "C:\Windows\SYSVOL" `
  -InstallDns:$true `
  -Force:$true

#Note: During execution, you will be prompted to enter and confirm the Safe Mode Password (Directory Services Restore Mode / DSRM password). Store this password securely.


# Promote to Additional Domain Controller
Install-ADDSDomainController `
  -DomainName "yourdomain.com" `
  -Credential (Get-Credential) `
  -DatabasePath "C:\Windows\NTDS" `
  -LogPath "C:\Windows\NTDS" `
  -SYSVOLPath "C:\Windows\SYSVOL" `
  -InstallDns:$true `
  -Force:$true
