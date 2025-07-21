
○ Get-Mailbox -Database "Mailbox Database 2106985689" -AuditLog | New-MoveRequest -TargetDatabase "StaffDB"

○ Get-Mailbox -Database "Mailbox Database 2106985689" -Monitoring | New-MoveRequest -TargetDatabase "StaffDB"

○ Get-Mailbox -Database "Mailbox Database 2106985689" -Archive | New-MoveRequest -TargetDatabase "StaffDB"

○ Get-Mailbox -Database "Mailbox Database 2106985689" -Arbitration | New-MoveRequest -TargetDatabase "StaffDB"

○ Get-Mailbox -Database "Mailbox Database 2106985689" -ReadFromDomainController | New-MoveRequest -TargetDatabase "StaffDB"

Get-Mailbox -ResultSize Unlimited | Where-Object {$_.Database -like "*2106985689*"} | Select Name,Alias,Database






Move-ActiveMailboxDatabase DB02 -ActivateOnServer PROEX02 -SkipLagChecks -MountDialOverride:BestEffort

Suspend-MailboxDatabaseCopy DB02\DR-EX01
Remove-MailboxDatabaseCopy DB02\DR-EX01

Get-MailboxDatabaseCopyStatus DB02\* | fl Name,Status,CopyQueueLength,ReplayQueueLength,ContentIndexState



# KTonhToy
*Run Exchange Management Shell as Administrator "PROEX02"	
	
Phase 1: Veritifcation Activities	
	
Test-ServiceHealth	
	
Test-ReplicationHealth	
	
Get-ClusterNode	
	
Get-ServerComponentState -Identity PROEX02
	
Get-DatabaseAvailabilityGroup *	
	
Get-MailboxDatabaseCopyStatus -Server PROEX02	
-----------------------------------------------------------------------------------------------	
Phase 2: Put Server Maintenace Mode	
	
Set-ServerComponentState PROEX02 –Component HubTransport –State Draining –Requester Maintenance	
	
Redirect-Message -Server PROEX02 -Target PROEX02.bywoon.local	
	
Suspend-ClusterNode –Name PROEX02
	
Set-MailboxServer PROEX02 -DatabaseCopyActivationDisabledAndMoveNow $true	
	
Set-MailboxServer PROEX02 –DatabaseCopyAutoActivationPolicy Blocked	
	
Set-ServerComponentState PROEX02 –Component ServerWideOffline –State InActive –Requester Maintenance	
	
Restart	

Then after restart : 
- CMD admin > cd C:\Install\patchfile.exe
----------------------------------------------------------------------------------------------	
Phase 3: Take out server from maintenance mode	
	
Set-ServerComponentState PROEX02 –Component ServerWideOffline –State Active –Requester Maintenance	
	
Resume-ClusterNode –Name PROEX02
	
Set-MailboxServer PROEX02 –DatabaseCopyAutoActivationPolicy Unrestricted	
	
Set-MailboxServer PROEX02 –DatabaseCopyActivationDisabledAndMoveNow $false	
	
Set-ServerComponentState PROEX02 –Component HubTransport –State Active –Requester Maintenance	
----------------------------------------------------------------------------------------------	
Phase 4: Veritifcation Activities	
	
Test-ServiceHealth	
	
Test-ReplicationHealth	
	
Get-ClusterNode	
	
Get-ServerComponentState -Identity PROEX02
	
Get-DatabaseAvailabilityGroup *	
	
Get-MailboxServer PROEX02 | Select DatabaseCopyAutoActivationPolicy	
