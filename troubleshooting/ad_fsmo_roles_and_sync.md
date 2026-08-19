# Verify FSMO domain controllers roles

netdom query fsmo

#######################################################################################

# Troubleshoot Active Directory FSMO synchronization problems

## Show which domain controller the pc is connected
echo %logonserver%

## Show FSMO synchronization problems between domain controllers
repadmin /replsummary
repadmin /showrepl

## Force replication between all Domain Controllers
repadmin /syncall /AdeP
