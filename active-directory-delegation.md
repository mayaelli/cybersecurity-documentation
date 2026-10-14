# Active Directory Delegation of Control (TryHackMe AD Basics)

---

## Objective

Understand how **Delegation of Control** works in Active Directory.

Instead of making a user a **Domain Admin**, we can grant them specific administrative permissions.

This follows the principle of:

```text
Least Privilege
```

Meaning:

> Give users only the permissions necessary to perform their job.

---

## Learning Outcomes

By the end of this exercise:

- Understand Delegation
- Understand Least Privilege
- Reset a user's password without Domain Admin privileges
- Use PowerShell to perform delegated administrative tasks

---

## Concept Review

### Domain Admin

```text
Full control over the domain
```

Can:

- Manage users
- Manage computers
- Reset passwords
- Configure Active Directory
- Administer Domain Controllers

---

### Delegation

```text
Granting a user a specific administrative task
without making them a Domain Admin.
```

Example:

```text
Phillip
↓
Can reset Sophie's password
↓
Cannot administer the whole domain
```

---

## OU vs Security Group vs Delegation

### OU

```text
Policies
```

Examples:

- Desktop settings
- Password policies
- Security policies

---

### Security Group

```text
Permissions
```

Examples:

- Shared folders
- Printers
- Applications

---

### Delegation

```text
Administrative Tasks
```

Examples:

- Reset passwords
- Unlock accounts
- Manage users

---

# Delegating Password Reset Rights

### Navigate to

```text
Active Directory Users and Computers
```

Right-click the target OU:

```text
Sales
→ Delegate Control
```

---

### Step 1 - Delegate Control

<img width="311" height="372" alt="image" src="https://github.com/user-attachments/assets/3cf7e413-2308-4a80-bd8c-7a070fe4b0e9" />


Select:

```text
Delegate Control...
```

---

### Step 2 - Add Phillip

<img width="583" height="570" alt="image" src="https://github.com/user-attachments/assets/be38ca4d-3cd1-4f38-be1f-c2452987f543" />


Add:

```text
Phillip
```

Click:

```text
Check Names
```

Then:

```text
OK
```

---

### Step 3 - Delegate Password Reset

<img width="531" height="414" alt="image" src="https://github.com/user-attachments/assets/a4fd2c21-4ae1-4b52-8ec3-48b476aec6f9" />


Select:

```text
Reset user passwords and force password change at next logon
```

Proceed through the wizard.

---

# Logging in as Phillip

Use RDP credentials:

```text
Username: THM\phillip
Password: Claire2008
```

---

# Reset Sophie's Password

Open PowerShell as Phillip.

### Command

```powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

Example password:

```text
ChangeMeNow@2023
```

---

# Force Password Change At Next Logon

```powershell
Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose
```

---

# Lab Issues Encountered

## Issue 1 - Invalid Credentials

<img width="1917" height="1082" alt="image" src="https://github.com/user-attachments/assets/43031b5c-6363-4c5f-aaea-e6b047aa05bb" />
<img width="1917" height="982" alt="image" src="https://github.com/user-attachments/assets/89a807bc-8d08-4d93-9344-0e7996dfbbab" />


### Error

```text
The logon attempt failed.
```

### Observed While Attempting

```text
THM\sophie
```

### Possible Causes

- Incorrect password entry
- Password not reset successfully
- Password complexity issues

---

## Issue 2 - RDP Session Limit

### Error

```text
The number of connections to this computer is limited
and all connections are in use right now.
```

### Observations

- Authentication may have succeeded
- System rejected connection due to session limits
- Different from a password failure

---

## Issue 3 - RunAs Failure

### Attempt

```powershell
runas /user:THM\phillip powershell
```

### Error

```text
1385: Logon failure:
the user has not been granted the requested logon type.
```

### Explanation

```text
Phillip is not permitted
to perform interactive RunAs logons.
```

---

# Troubleshooting Password Resets

## Password Reset Repeatedly Failed

### Commands Tested

```powershell
Set-ADAccountPassword -Identity sophie -Reset -NewPassword (
    ConvertTo-SecureString 'Sophie2022!' -AsPlainText -Force
)
```

```powershell
Set-ADAccountPassword -Identity sophie -Reset -NewPassword (
    ConvertTo-SecureString 'sophieELLI00!' -AsPlainText -Force
)
```

```powershell
Set-ADAccountPassword -Identity sophie -Reset -NewPassword (
    ConvertTo-SecureString 'ChangeMeNow@2023' -AsPlainText -Force
)
```

---

### Error Received

```text
Set-ADAccountPassword : The password does not meet
the length, complexity, or history requirement
of the domain.

ADPasswordComplexityException
```

---

### Initial Assumption

```text
Password not complex enough
```

---

### Passwords Already Met Complexity Rules

✅ Uppercase letters

✅ Lowercase letters

✅ Numbers

✅ Special characters

✅ Sufficient length

Examples:

```text
ChangeMeNow@2023
ComplexPassword!456
Sophie00!@0099
```

---

### Revised Understanding

Possible causes:

```text
Password History Restriction
```

or

```text
Additional Domain Password Policies
```

The domain may prevent reuse of previously used passwords.

---

### Important Lesson Learned

When Active Directory returns:

```text
ADPasswordComplexityException
```

it does **not always mean password complexity**.

It may also indicate:

- Password history violation
- Password policy violation
- Domain-specific restrictions

---

# Key Takeaways

```text
OU
→ Policies

Security Group
→ Resource Access

Delegation
→ Administrative Tasks

Domain Admin
→ Full Control

Least Privilege
→ Minimum permissions required
```

---

# Personal Notes

The hardest part of this lab was not Active Directory itself.

The largest challenge was understanding the authentication flow and RDP session behavior.

Despite the issues encountered, the main lesson was successfully understood:

```text
Delegation allows a user
to perform a specific administrative task
without becoming a Domain Admin.
```

---

# Frustration Level

```text
████████████████████████████ 100%
```

> "Complexity errors don't always mean complexity problems."

# GPO (Group Policy Objects)
## Core Concept
```
OU = WHO gets the rule
GPO = THE rule itself
```
```
Examples
Marketing OU
↓
GPO: No Control Panel
↓
Marketing users cannot access Control Panel

Domain
↓
GPO: Auto Lock Screen (5 min)
↓
Applies to all computers
Key Terms
Plain Text
GPO
→ Collection of policies/settings
```
```
OU
→ Target audience
 
Security Filtering
→ Apply GPO only to selected users/computers
 
SYSVOL
→ Network share storing/distributing GPOs
 
gpupdate /force
→ Refresh policies immediately
Relationships
Plain Text
OU
→ Who
 
GPO
→ Rule
 
Security Group
→ Access
 
Delegation
→ Admin task
Cheat Code
Plain Text
Create GPO
↓
Link GPO to OU
↓
Policy applies
```

# Authentication Methods (AD)
## Kerberos
```
Modern
Default
Ticket-based authentication
```

Think:
```
Theme Park Ticket 🎟️
Kerberos Flow
```

Login
↓
Get TGT
↓
Use TGT to get TGS
↓
Access service
Key Terms
Plain Text
TGT
(Ticket Granting Ticket)
→ Ticket that gets more tickets
 
TGS
(Ticket Granting Service)
→ Ticket for a specific service
 
KDC
(Key Distribution Center)
→ Issues Kerberos tickets
Easy Memory
```
TGT
→ Ticket to get tickets
 
TGS
→ Ticket to access service
NetNTLM
```
Legacy
Challenge-Response authentication

Think:

Server:
Prove who you are.
 
Client:
Challenge response.
 
DC:
Checks answer.
Flow
```
Challenge
↓
Response
↓
Verification
↓
Access
Important

```
Password is NEVER sent over the network
```
Kerberos vs NetNTLM

Kerberos
→ Tickets
→ Modern
→ Default
 
NetNTLM
→ Challenge/Response
→ Legacy
→ Compatibility
Exam Triggers

Default authentication?
→ Kerberos
 
Ticket for more tickets?
→ TGT
 
Ticket for service access?
→ TGS
 
Password sent in NetNTLM?
→ No
 
GPO stored/distributed by?
→ SYSVOL
 
Force GPO refresh?
→ gpupdate /force

## Ultimate AD Cheat Sheet
```
OU
→ Who gets the policy
 
GPO
→ Policy
 
Security Group
→ Resource Access
 
Delegation
→ Admin Tasks
 
Domain Admin
→ Full Control
 
Least Privilege
→ Minimum permissions
 
Kerberos
→ Tickets
 
NetNTLM
→ Challenge/Response
 
SYSVOL
→ GPO Share
 
gpupdate /force
→ Refresh GPOs
```
