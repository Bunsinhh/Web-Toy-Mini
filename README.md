YES bro! 🔥🔥 That’s the **best possible news**.

---

### ✅ The Deleted Database Files **Still Exist** on Disk:

You still have:

* `Mailbox Database 2106985689` folder under
  `C:\Program Files\Microsoft\Exchange Server\V15\Mailbox\`
* That means the **.edb file and logs are still there**
* You can now **recreate the database shell in Exchange** and **mount it**

---

## 🛠 How to Bring It Back (Right Now)

### 1. Run This in Exchange Management Shell:

```powershell
New-MailboxDatabase -Name "Mailbox Database 2106985689" `
-Server "DR-EX01" `
-EdbFilePath "C:\Program Files\Microsoft\Exchange Server\V15\Mailbox\Mailbox Database 2106985689\Mailbox Database 2106985689.edb" `
-LogFolderPath "C:\Program Files\Microsoft\Exchange Server\V15\Mailbox\Mailbox Database 2106985689"
```

> Make sure the paths match **exactly** as shown in your screenshot.

---

### 2. Then Mount It:

```powershell
Mount-Database "Mailbox Database 2106985689"
```

---

### 3. Confirm It’s Back

Run:

```powershell
Get-MailboxDatabase -Identity "Mailbox Database 2106985689"
```

If successful, you’ve **restored the deleted DB** without needing a backup. Boom. 💥

---

## ✅ Then: Move Mailboxes Out or Leave It Be

Now you can:

* List mailboxes inside:

```powershell
Get-Mailbox -Database "Mailbox Database 2106985689"
```

* Move them to `DB02` safely:

```powershell
Get-Mailbox -Database "Mailbox Database 2106985689" | New-MoveRequest -TargetDatabase DB02
```

---

Let me know if you want me to verify the exact `New-MailboxDatabase` command with your paths — but you’re 1 step away from solving everything.


-----------------------------------------------------------------------------
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
