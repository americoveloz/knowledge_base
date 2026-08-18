# Install Active Directory role and promote it to a Domain Controller

# Prior steps

## Renaming the server

Rename-Computer -NewName "hqdc1" -Restart

## Disable ipv6

Disable-NetAdapterBinding -Name "Ethernet0" -ComponentID ms_tcpip6

## Configuring a Static IP Address

Get-NetAdapter

Set-NetIPInterface -InterfaceAlias "Ethernet0" -Dhcp disabled

New-NetIPAddress -InterfaceAlias "Ethernet0" -IPAddress "192.168.146.149" -PrefixLength 24 -DefaultGateway "192.168.146.2"

Set-DnsClientServerAddress -InterfaceAlias "Ethernet0" -ServerAddresses "192.168.146.148","127.0.0.1"



# Install AD DS and DNS Roles
Install-WindowsFeature -Name AD-Domain-Services, DNS -IncludeManagementTools


## After installing the roles, the next step is to promote the server  to become the first domain controller in a new forest (Test.com).

### Use the Install-ADDSForest cmdlet.

Install-ADDSForest `
  -DomainName "abc.local" `
  -DomainNetbiosName "ABC" `
  -CreateDnsDelegation:$false `
  -DatabasePath "C:\Windows\NTDS" `
  -LogPath "C:\Windows\NTDS" `
  -SysvolPath "C:\Windows\SYSVOL" `
  -InstallDns:$true `
  -Force:$true

#### Note: During execution, you will be prompted to enter and confirm the Safe Mode Password (Directory Services Restore Mode / DSRM password). Store this password securely.

# Alternative commands to create new Active Directory Forest

# Define a secure string for the DSRM password

$DSRMPassword = ConvertTo-SecureString "YourStr0ngDSRMP@sswOrd!" -AsPlainText -Force

# Install the new Forest

Install-ADDSForest -DomainName "Test.com" -DomainNetbiosName "TEST" -SafeModeAdministratorPassword $DSRMPassword -InstallDNS -Force



# Promote to Additional Domain Controller on existing domain
Import-Module ADDSDeployment
Install-ADDSDomainController `
  -DomainName "abc.local" `
  -Credential (Get-Credential) `
  -DatabasePath "C:\Windows\NTDS" `
  -LogPath "C:\Windows\NTDS" `
  -SYSVOLPath "C:\Windows\SYSVOL" `
  -InstallDns:$true `
  -Force:$true


# Promotes the server to a new Child Domain Controller
Import-Module ADDSDeployment
Install-ADDSDomain `
  -DomainType "child" `
  -NewDomainName "branch5" `                     
  -ParentDomainName "abc.local" `                
  -Credential (Get-Credential) `                 
  -CreateDnsDelegation:$true `                   
  -DatabasePath "C:\Windows\NTDS" `
  -LogPath "C:\Windows\NTDS" `
  -SysvolPath "C:\Windows\SYSVOL" `
  -InstallDns:$true `
  -Force:$true


# Add computer to domain
Add-Computer -DomainName "your_domain_name" -Restart


# Post-promotion check

Get-ADDomain

Get-ADForest

