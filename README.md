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


You're almost done, bro — this last error is super clear and easy to fix:

---

## ❗ Error:

> `"The operation couldn't be performed because 'TargetMailboxAlias' couldn't be found."`

### 🔎 Meaning:

You used a **placeholder name** (`TargetMailboxAlias`), but Exchange tried to find a real mailbox with that name — and it doesn’t exist.

---

## ✅ Fix: Use the **Real Existing Target Mailbox**

### Step 1: See Which Mailboxes Exist

Run this to list all active mailboxes:

```powershell
Get-Mailbox | Select Name,Alias,UserPrincipalName
```

Pick one you want to **restore into**, for example:

| Name     | Alias   | UserPrincipalName                                         |
| -------- | ------- | --------------------------------------------------------- |
| John Doe | `jdoe`  | [john.doe@yourdomain.com](mailto:john.doe@yourdomain.com) |
| Admin    | `admin` | [admin@yourdomain.com](mailto:admin@yourdomain.com)       |

---

### Step 2: Re-run with Correct Mailbox

If `admin` is your existing live mailbox, run:

```powershell
New-MailboxRestoreRequest `
-SourceDatabase "Mailbox Database 2106985689" `
-SourceStoreMailbox 5cbf202e-5f81-4fb5-8aff-219d7c67d171 `
-TargetMailbox admin
```

> 🟡 No quotes needed around the GUID or alias unless it contains spaces.

---

### 🧠 Pro Tip:

* The **target mailbox** must be in a mounted active DB (e.g. `DB02`)
* If you're trying to **restore to the same original mailbox**, it must already exist

---

Let me know the output of `Get-Mailbox` if you're unsure which one to use. I can copy-paste the exact working command for you 🔧

