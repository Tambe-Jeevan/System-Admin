# Day 07 — Active Directory PowerShell: Managing Users and Accounts

System Administrator Journey · Month 1 · Active Directory Fundamentals

(Today's focus: PowerShell + Active Directory)

For the last few days, you've learned how Active Directory works, including Domain Controllers, FSMO roles, `NTDS.dit`, SYSVOL, CSVDE and LDIFDE.

Today we'll begin one of the most important practical skills for a System Administrator: **managing Active Directory through PowerShell**.

By the end of this lesson, you'll understand how to:

1. Connect PowerShell to Active Directory.
2. Search for users and view their properties.
3. Create a test user account.
4. Disable and enable an account.
5. Reset a user's password.

We'll do everything in your isolated `lab.local` environment.

## 1. First, what is PowerShell?

PowerShell is a command-line shell and scripting language developed by Microsoft.

You can use it to manage Windows, servers, services, users, groups, networking and many other IT tasks.

Think about two ways to perform the same task:

**Method 1 — GUI**

Open ADUC → Find user → Right-click → Reset Password → Confirm.

Useful for individual tasks.

---

**Method 2 — PowerShell**

Run a command to reset a user's password.

Useful for repeatable and automated tasks.

**Real company example:** Imagine your service desk receives 50 password-reset requests. PowerShell can help administrators perform repetitive operations efficiently, provided the correct permissions and approval process are in place.

## 2. What is the Active Directory PowerShell module?

PowerShell by itself does not automatically know every Active Directory command.

Microsoft provides a collection of AD-specific commands called the **ActiveDirectory PowerShell module**.

A module is a collection of commands (called cmdlets) that adds functionality to PowerShell.

Examples:

| Cmdlet                  | Purpose                             |
| ----------------------- | ----------------------------------- |
| `Get-ADUser`            | Search and view user accounts       |
| `New-ADUser`            | Create a user account               |
| `Set-ADUser`            | Modify user properties              |
| `Disable-ADAccount`     | Disable an account                  |
| `Enable-ADAccount`      | Enable an account                   |
| `Remove-ADUser`         | Delete a user account               |
| `Set-ADAccountPassword` | Reset or change an account password |

We'll learn these one by one rather than memorising them.

## 3. Lab preparation — Check the AD module

Open **Windows PowerShell as Administrator** inside your `LAB-DC01` VM.

### Step 1: Check whether the module is installed

```
Get-Module -ListAvailable ActiveDirectory
```

If installed, PowerShell should display information about the ActiveDirectory module.

### Step 2: Load the module

```
Import-Module ActiveDirectory
```

What does this mean?

- `Import-Module` loads a module into your current PowerShell session.
- `ActiveDirectory` is the module name.
- It makes the AD cmdlets available in that session.

### Step 3: Verify the module

```
Get-Command -Module ActiveDirectory
```

This displays commands provided by the ActiveDirectory module.

You should see commands such as `Get-ADUser`, `New-ADUser` and `Set-ADUser`.

**If the module is not found:** Check whether the AD DS management tools/RSAT components are installed. On your domain controller, the module is normally available with the AD DS management tools.

## 4. Understand the basic PowerShell command structure

Before running AD commands, understand this pattern:

```
Verb-Noun
```

Examples:

- `Get-ADUser` — Get = action, ADUser = object.
- `New-ADUser` — New = create, ADUser = object.
- `Set-ADUser` — Set = modify, ADUser = object.
- `Remove-ADUser` — Remove = delete, ADUser = object.

This naming pattern is common in PowerShell.

Another important concept is the **parameter**. A parameter gives additional information to a command.

For example:

```
Get-ADUser -Identity "Administrator"
```

Here:

- `Get-ADUser` is the cmdlet.
- `-Identity` is a parameter.
- `"Administrator"` is the value supplied to that parameter.

## 5. Lab 1 — Search for Active Directory users

Let's start with read-only commands. They will not change anything in AD.

### Step 1: Search for the Administrator account

```
Get-ADUser -Identity "Administrator"
```

This retrieves the AD user object whose identity is `Administrator`.

### Step 2: Display all user properties

```
Get-ADUser -Identity "Administrator" -Properties *
```

Why `-Properties *`?

By default, `Get-ADUser` returns a limited set of properties. The `-Properties *` parameter requests all available properties for that user.

### Step 3: Display selected properties

```
Get-ADUser -Identity "Administrator" -Properties Department,Title,EmailAddress |
Select-Object Name,SamAccountName,Enabled,Department,Title,EmailAddress
```

This is useful because you usually don't need to display every property.

### Step 4: Search for all users

```
Get-ADUser -Filter *
```

This retrieves all users visible in the search scope.

To display a cleaner table:

```
Get-ADUser -Filter * |
Format-Table Name,SamAccountName,Enabled
```

### Step 5: Search for a particular username

```
Get-ADUser -Filter "SamAccountName -like '*admin*'"
```

This searches for users whose `SamAccountName` contains `admin`.

**Remember:** `SamAccountName` is a user's traditional Windows logon name. It is not necessarily the same as their full name or email address.

## 6. Lab 2 — Understand where users are stored

Active Directory objects are organised using a directory structure.

For example:

```
lab.local
│
├── Builtin
├── Computers
├── Domain Controllers
├── Users
└── LabUsers
```

`LabUsers` will be a new Organizational Unit (OU) that we create for today's practical exercise.

An OU is a container used to organise AD objects. Administrators can also link Group Policy Objects to OUs and delegate administrative permissions.

First, check your domain:

```
Get-ADDomain | Select-Object DNSRoot,DistinguishedName
```

Expected values include:

```
DNSRoot            : lab.local
DistinguishedName  : DC=lab,DC=local
```

Now check whether the OU already exists:

```
Get-ADOrganizationalUnit -Filter "Name -eq 'LabUsers'"
```

If it doesn't exist, create it:

```
New-ADOrganizationalUnit `
-Name "LabUsers" `
-Path "DC=lab,DC=local" `
-ProtectedFromAccidentalDeletion $true
```

What each parameter means:

- `-Name` — the OU name.
- `-Path` — where the OU should be created.
- `-ProtectedFromAccidentalDeletion $true` — enables protection against accidental deletion.

Verify:

```
Get-ADOrganizationalUnit -Filter "Name -eq 'LabUsers'"
```

## 7. Lab 3 — Create your first AD user using PowerShell

(Creates a new test account)

Now we'll create a test user inside the `LabUsers` OU.

Use a lab-only account, not a real company account.

### Step 1: Prepare a password securely

```
$Password = Read-Host "Enter a password for the test account" -AsSecureString
```

PowerShell will prompt you for the password. The `-AsSecureString` parameter helps prevent it from being displayed as plain text.

Choose a strong password that meets your domain password policy.

### Step 2: Create the user

```
New-ADUser `
-Name "Jeevan Lab User" `
-GivenName "Jeevan" `
-Surname "Lab" `
-SamAccountName "jeevan.lab01" `
-UserPrincipalName "jeevan.lab01@lab.local" `
-Department "IT" `
-Title "IT Support Engineer" `
-Path "OU=LabUsers,DC=lab,DC=local" `
-AccountPassword $Password `
-Enabled $true
```

Let's understand the important parameters:

| Parameter            | Meaning                         |
| -------------------- | ------------------------------- |
| `-Name`              | Display name of the user        |
| `-GivenName`         | First name                      |
| `-Surname`           | Last name                       |
| `-SamAccountName`    | Traditional logon username      |
| `-UserPrincipalName` | UPN, often used to sign in      |
| `-Department`        | Department attribute            |
| `-Title`             | Job title attribute             |
| `-Path`              | OU where the account is created |
| `-AccountPassword`   | Initial password                |
| `-Enabled`           | Enables the account             |

The backtick character `` ` `` at the end of each line tells PowerShell that the command continues on the next line. It must be the final character on that line—no trailing spaces after it.

### Step 3: Verify the user

```
Get-ADUser -Identity "jeevan.lab01" -Properties *
```

For a cleaner view:

```
Get-ADUser -Identity "jeevan.lab01" -Properties Department,Title |
Select-Object Name,SamAccountName,UserPrincipalName,Enabled,Department,Title
```

You should see the account with `Enabled` set to `True`.

**If you get an error:** Don't repeat the creation command blindly. First check whether the account already exists:

```
Get-ADUser -Identity "jeevan.lab01"
```

## 8. Lab 4 — Disable and enable an AD user

This is a very common service desk and System Administrator task.

**Real company example:** An employee leaves the organisation. After the approved offboarding request, IT disables their AD account to prevent further domain sign-ins.

### Step 1: Disable the test user

```
Disable-ADAccount -Identity "jeevan.lab01"
```

### Step 2: Verify the account status

```
Get-ADUser -Identity "jeevan.lab01" |
Select-Object Name,SamAccountName,Enabled
```

Expected:

```
Name             SamAccountName   Enabled
----             --------------   -------
Jeevan Lab User  jeevan.lab01     False
```

### Step 3: Enable the test user again

```
Enable-ADAccount -Identity "jeevan.lab01"
```

Verify:

```
Get-ADUser -Identity "jeevan.lab01" |
Select-Object Name,SamAccountName,Enabled
```

Expected: `Enabled` is `True`.

**Important:** Disabling an AD account is not the same as deleting it. The user object remains in Active Directory.

## 9. Lab 5 — Reset the user's password

Imagine an employee contacts the service desk because they have forgotten their password.

After verifying the employee's identity and following the company's password-reset procedure, an authorised administrator can reset it.

For our test account, run:

```
$NewPassword = Read-Host "Enter the new test password" -AsSecureString
```

Then:

```
Set-ADAccountPassword `
-Identity "jeevan.lab01" `
-Reset `
-NewPassword $NewPassword
```

What does `-Reset` mean?

It tells PowerShell to reset the password rather than requiring the user's current password.

To require a password change at the next sign-in:

```
Set-ADUser -Identity "jeevan.lab01" -ChangePasswordAtLogon $true
```

Verify the account:

```
Get-ADUser -Identity "jeevan.lab01" -Properties PasswordLastSet,pwdLastSet |
Select-Object Name,Enabled,PasswordLastSet
```

A password reset changes password-related information, but it does not automatically enable a disabled account.

**Security note:** Use these commands only on your lab account today. In a company environment, follow identity verification, approval and auditing procedures. Don't place real employee passwords in scripts or command history.

## 10. Day 07 command revision sheet

| Command                         | What it does                 |
| ------------------------------- | ---------------------------- |
| `Import-Module ActiveDirectory` | Loads AD commands            |
| `Get-ADUser`                    | Searches for a user          |
| `New-ADUser`                    | Creates a user               |
| `Set-ADUser`                    | Updates user properties      |
| `Disable-ADAccount`             | Disables an account          |
| `Enable-ADAccount`              | Enables an account           |
| `Set-ADAccountPassword`         | Resets or changes a password |
| `Get-ADDomain`                  | Displays domain information  |
| `New-ADOrganizationalUnit`      | Creates an OU                |

A good System Administrator doesn't just memorise commands. They understand what an action changes, who is authorised to perform it, and how to verify the result.

## 11. Day 07 practical checklist

**Lab progress**

## /

Your Day 07 PDF guide:

[Download Day 07 — Active Directory PowerShell Lab Guide]\(/mnt/data/SysAdmin_Day07_Active_Directory_PowerShell_Lab_Guide.pdf)

**What I need from you:** Start with the module check and `Get-ADUser` commands. Share your outputs or screenshots, especially if you encounter an error. We'll troubleshoot together before proceeding to Day 08.