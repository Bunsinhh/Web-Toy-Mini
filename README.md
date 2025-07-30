Import-Module ActiveDirectory

# Domain root
$root = "DC=mrkn8,DC=local"

function New-OU {
    param(
        [string]$Name,
        [string]$Parent
    )
    $path = if ($Parent) { "OU=$Name,$Parent" } else { "OU=$Name,$root" }
    if (-not (Get-ADOrganizationalUnit -Filter "DistinguishedName -eq '$path'" -ErrorAction SilentlyContinue)) {
        New-ADOrganizationalUnit -Name $Name -Path ($Parent ? $Parent : $root) | Out-Null
        Write-Host "Created OU: $path"
    } else {
        Write-Host "Exists:     $path"
    }
    return $path
}

# 1. Top-level OUs
$ouAdminAccounts      = New-OU -Name "AdminAccounts"      -Parent $null
$ouServiceAccounts    = New-OU -Name "ServiceAccounts"    -Parent $null
$ouServiceInfra       = New-OU -Name "ServiceInfrastructure" -Parent $null
$ouUsers              = New-OU -Name "Users"              -Parent $null
$ouComplianceSystems  = New-OU -Name "ComplianceSystems"  -Parent $null
$ouComputers          = New-OU -Name "Computers"          -Parent $null
$ouBranches           = New-OU -Name "Branches"           -Parent $null
$ouGroups             = New-OU -Name "Groups"             -Parent $null

# 2. AdminAccounts children
New-OU -Name "DomainAdmins"   -Parent $ouAdminAccounts
New-OU -Name "SecurityAdmins" -Parent $ouAdminAccounts

# 3. ServiceInfrastructure children
New-OU -Name "CoreBankingApps"  -Parent $ouServiceInfra
New-OU -Name "PaymentGateway"   -Parent $ouServiceInfra
New-OU -Name "SWIFTInterop"     -Parent $ouServiceInfra

# 4. Users by business lines & functions
$newUsers = @{
    "RetailBanking"     = $ouUsers
    "CorporateBanking"  = $ouUsers
    "WealthManagement"  = $ouUsers
    "Treasury"          = $ouUsers
    "Finance"           = $ouUsers
    "Risk"              = $ouUsers
    "Compliance"        = $ouUsers
    "IT"                = $ouUsers
    "CustomerService"   = $ouUsers
    "Executives"        = $ouUsers
}
foreach ($name in $newUsers.Keys) {
    New-OU -Name $name -Parent $newUsers[$name]
}

# 5. ComplianceSystems children
New-OU -Name "AuditServers"        -Parent $ouComplianceSystems
New-OU -Name "LoggingWorkstations" -Parent $ouComplianceSystems

# 6. Computers subdivision
$ouServers      = New-OU -Name "Servers"      -Parent $ouComputers
$ouWorkstations = New-OU -Name "Workstations" -Parent $ouComputers

#    Servers types
New-OU -Name "DomainControllers" -Parent $ouServers
New-OU -Name "FileServers"       -Parent $ouServers
New-OU -Name "ApplicationServers"-Parent $ouServers

#    Workstation types
New-OU -Name "Corporate" -Parent $ouWorkstations
New-OU -Name "Cubicles"  -Parent $ouWorkstations

# 7. Branches
New-OU -Name "HeadOffice"        -Parent $ouBranches
New-OU -Name "Branch001-Downtown"-Parent $ouBranches
New-OU -Name "Branch002-Uptown"  -Parent $ouBranches
New-OU -Name "RegionalOffices"   -Parent $ouBranches

# 8. Groups
New-OU -Name "SecurityGroups"     -Parent $ouGroups
New-OU -Name "DistributionGroups" -Parent $ouGroups

Write-Host "`nOU structure build complete."
