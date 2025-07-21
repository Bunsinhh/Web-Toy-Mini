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
---------------------------

Great question bro — you're this close to finishing it off. Let me explain 🔍

---

## ❓ What the Command Does:

You tried:

```powershell
New-MailboxRestoreRequest -SourceDatabase "Mailbox Database 2106985689" -SourceStoreMailbox -Target ...
```

But Exchange threw:

> ❌ **Missing an argument for parameter 'SourceStoreMailbox'**

---

### ✅ What It Was Supposed to Do

That command is used to **restore a mailbox** from your **recovery database** (`Mailbox Database 2106985689`) into a live mailbox.

To do that, you must provide:

* `-SourceStoreMailbox` → the **Mailbox GUID** or display name from the recovery DB
* `-TargetMailbox` → the existing **target mailbox** you want to restore into

---

## 🔧 How to Fix It

### 1. Get Source Mailbox Details

Run this to list all mailboxes in the recovery DB with their GUIDs:

```powershell
Get-MailboxStatistics -Database "Mailbox Database 2106985689" | Select DisplayName,MailboxGuid
```

Let’s say it shows:

```text
DisplayName                          MailboxGuid
------------                         ------------------------------------
HealthMailbox-DR-EX01-Mailbox...     9a3b7c8d-eed3-4fdd-8e92-xxxxxx
```

---

### 2. Restore That Mailbox (Example)

If you want to restore **that HealthMailbox** into a mailbox called `Admin`:

```
New-MailboxRestoreRequest -SourceDatabase "Mailbox Database 2106985689" -SourceStoreMailbox 5cbf202e-5f81-4fb5-8aff-219d7c67d171 -TargetMailbox TargetMailboxAlias

```

---

### 🧠 Reminder:

* `SourceStoreMailbox` must be a **MailboxGuid**
* `TargetMailbox` must already exist in **your active database (`DB02`)**

---

## 🧪 You Can Also Restore SystemMailbox (if needed)

```powershell
New-MailboxRestoreRequest `
-SourceDatabase "Mailbox Database 2106985689" `
-SourceStoreMailbox bca0dd1d-dcaf-4c71-a7a9-198a5927a024 `
-TargetMailbox SomeMailboxAlias
```

---

Let me know the mailbox you want to recover, and I’ll help you craft the exact restore command 💪
