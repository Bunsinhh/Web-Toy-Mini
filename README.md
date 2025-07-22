Get-Mailbox -Database "Mailbox Database 2106985689" -AuditLog | New-MoveRequest -TargetDatabase "DB02"
Get-Mailbox -Database "Mailbox Database 2106985689" -Monitoring | New-MoveRequest -TargetDatabase "DB02"
Get-Mailbox -Database "Mailbox Database 2106985689" -Archive | New-MoveRequest -TargetDatabase "DB02"
Get-Mailbox -Database "Mailbox Database 2106985689" -Arbitration | New-MoveRequest -TargetDatabase "DB02"
Get-Mailbox -Database "Mailbox Database 2106985689" -ReadFromDomainController | New-MoveRequest -TargetDatabase "DB02"
optional
Get-Mailbox -Database "Mailbox Database 2106985689" | New-MoveRequest -TargetDatabase "DB02"
