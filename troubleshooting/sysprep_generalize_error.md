# Sysprep shows an error when trying to generalize Windows Server VM to create a template.

# Problem

When trying to use sysprep to create generalized Windows Server VM template with the following command, the command didn't work and presented an error:

C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown

Error: sysprep was not able to validate your windows installation

# Diagnostic

The logs in C:\Windows\System32\Sysprep\Panther\setupact.log shows the following

2026-08-15 11:15:35, Error                 SYSPRP Package Microsoft.MicrosoftEdge.Stable_151.0.4129.86_neutral__8wekyb3d8bbwe was installed for a user, but not provisioned for all users. This package will not function properly in the sysprep image.
2026-08-15 11:15:35, Error                 SYSPRP Failed to remove apps for the current user: 0x80073cf2.
2026-08-15 11:15:35, Error                 SYSPRP Exit code of RemoveAllApps thread was 0x3cf2.


# Solution

1. Run the following PowerShell cmdlets to list all the packages installed for the user:

PowerShell
Import-Module Appx
Import-Module Dism
Get-AppxPackage -AllUsers | Where PublisherId -eq 8wekyb3d8bbwe | Format-List -Property PackageFullName,PackageUserInformation

2. Remove Appx package by running the following cmdlet:

PowerShell
Remove-AppxPackage -Package <packagefullname>

3. Remove the provisioning by running the following cmdlet:

PowerShell
Remove-AppxProvisionedPackage -Online -PackageName <packagefullname>

Reference: https://learn.microsoft.com/en-us/troubleshoot/windows-client/setup-upgrade-and-drivers/sysprep-fails-remove-or-update-store-apps
