# Set domain root DN
$domain = "DC=mrknight,DC=local"  # CHANGE THIS to match your domain!

# Top-Level OU
New-ADOrganizationalUnit -Name "MrKnight" -Path $domain

# Tier 0: Admin Accounts
New-ADOrganizationalUnit -Name "AdminAccounts" -Path "OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "DomainAdmins" -Path "OU=AdminAccounts,OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "SecurityAdmins" -Path "OU=AdminAccounts,OU=MrKnight,$domain"

# Service Accounts
New-ADOrganizationalUnit -Name "ServiceAccounts" -Path "OU=MrKnight,$domain"

# Users and Departments
New-ADOrganizationalUnit -Name "Users" -Path "OU=MrKnight,$domain"
$departments = @("Finance", "Risk", "Compliance", "IT", "CustomerService", "Executives")
foreach ($dept in $departments) {
    New-ADOrganizationalUnit -Name $dept -Path "OU=Users,OU=MrKnight,$domain"
}

# Computers
New-ADOrganizationalUnit -Name "Computers" -Path "OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "Servers" -Path "OU=Computers,OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "Workstations" -Path "OU=Computers,OU=MrKnight,$domain"

# Branches
New-ADOrganizationalUnit -Name "Branches" -Path "OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "Cambodia" -Path "OU=Branches,OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "BTB" -Path "OU=Cambodia,OU=Branches,OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "BMC" -Path "OU=Cambodia,OU=Branches,OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "Korea" -Path "OU=Branches,OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "Busan" -Path "OU=Korea,OU=Branches,OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "Seoul" -Path "OU=Korea,OU=Branches,OU=MrKnight,$domain"

# Groups
New-ADOrganizationalUnit -Name "Groups" -Path "OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "SecurityGroups" -Path "OU=Groups,OU=MrKnight,$domain"
New-ADOrganizationalUnit -Name "DistributionGroups" -Path "OU=Groups,OU=MrKnight,$domain"
