Yup — I see it, bro. This time the error is:

---

### ❌ **NonMatchingLegacyDNPermanentException**

> *"Source mailbox’s legacyExchangeDN doesn't match the legacyExchangeDN or X500 proxy for target mailbox..."*

---

### 🔍 What It Means

Exchange is saying:

* The **mailbox you're restoring from** has an internal ID (legacyExchangeDN)
* But your **target mailbox (`Administrator`) doesn't match** that ID
* So Exchange blocks the restore to avoid corruption or confusion

---

## ✅ Fix: Add `-AllowLegacyDNMismatch` Switch

This tells Exchange:

> “I know the IDs don’t match, and I’m okay with that — just restore the mailbox anyway.”

---

### 🛠 Final Working Command for You:

```powershell
New-MailboxRestoreRequest `
-SourceDatabase "Mailbox Database 2106985689" `
-SourceStoreMailbox 5cbf202e-5f81-4fb5-8aff-219d7c67d171 `
-TargetMailbox Administrator `
-AllowLegacyDNMismatch
```

> 🔧 You can run it all in one line if needed:

```powershell
New-MailboxRestoreRequest -SourceDatabase "Mailbox Database 2106985689" -SourceStoreMailbox 5cbf202e-5f81-4fb5-8aff-219d7c67d171 -TargetMailbox Administrator -AllowLegacyDNMismatch
```

---

### ✅ After Running:

Check the restore status with:

```powershell
Get-MailboxRestoreRequest | Get-MailboxRestoreRequestStatistics
```

Once it’s completed, the mailbox content will be merged into `Administrator`.

---

Let me know if you want to restore another mailbox next — or clean up the recovery DB. You're right at the finish line! 🏁
