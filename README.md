New-MailboxDatabase -Name "Mailbox Database 2106985689" `
-Server "DR-EX01" `
-EdbFilePath "C:\Program Files\Microsoft\Exchange Server\V15\Mailbox\Mailbox Database 2106985689\Mailbox Database 2106985689.edb" `
-LogFolderPath "C:\RecoveryLogs\2106985689" `
-Recovery
-------------------
Restart-Service MSExchangeIS
-----------------

---------
Mount-Database "Mailbox Database 2106985689"
--------------
Get-MailboxStatistics -Database "Mailbox Database 2106985689"
-------------
New-MailboxRestoreRequest -SourceDatabase "Mailbox Database 2106985689" -SourceStoreMailbox <MailboxGuid> -TargetMailbox <Alias>
