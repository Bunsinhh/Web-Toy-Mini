Command
----------
Update-MailboxDatabaseCopy -Identity DB02\PROEX01
Update-MailboxDatabaseCopy -Identity DB02\DR-EX01
----------
Get-MailboxDatabaseCopyStatus DB02\* | ft Name,Status,CopyQueueLength,ReplayQueueLength,ContentIndexState -Auto
---------
Suspend-MailboxDatabaseCopy -Identity DB02\DR-EX01
Update-MailboxDatabaseCopy -Identity DB02\DR-EX01 -CatalogOnly:$false

