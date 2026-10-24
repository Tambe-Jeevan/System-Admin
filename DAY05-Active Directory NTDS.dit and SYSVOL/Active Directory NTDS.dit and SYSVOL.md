# Day 05 — Active Directory: `NTDS.dit` and SYSVOL

System Administrator Hands-on Journey · Month 1: Active Directory Fundamentals

Today's goal is to understand where Active Directory stores its database, where Group Policy files are stored, and how to verify that both are working on your Windows Server 2025 domain controller, `LAB-DC01`.

By the end, you should be able to explain these two important components in a real IT support or System Administrator interview.

## 1. Understand the big picture

Your Active Directory lab has a domain called `lab.local` and a domain controller named `LAB-DC01`.

LAB-DC01

Windows Server 2025 · Domain Controller

NTDS.dit

Active Directory database

Stores directory information such as users, groups, computers and other AD objects.

SYSVOL

Shared domain files

Stores domain Group Policy files and logon scripts that clients may need.

Remember the difference:

- NTDS.dit = directory database. It stores the actual Active Directory objects and their attributes.
- SYSVOL = shared files. It contains Group Policy files and scripts used across the domain.

They are both important, but they are not the same thing.

## 2. What is `NTDS.dit`?

`NTDS.dit` is the main database file used by Active Directory Domain Services (AD DS).

Think of it as a specialized directory database—not simply an ordinary Excel file or a folder of user accounts.

For example, when an administrator creates a user named `Jeevan`, Active Directory stores directory information about that user in its database.

That information can include:

- Username and account attributes
- Group membership
- Computer and organizational unit objects
- Security identifiers (SIDs)
- Other directory objects and their attributes

Passwords are not stored as plain-text passwords in this database. Active Directory stores password-related authentication data in protected forms.

Default database path:

`C:\Windows\NTDS\NTDS.dit`

The actual path can be different if AD DS was configured to use another location.

### Why does this matter to a System Administrator?

If Active Directory has database, storage, or integrity problems, domain operations may be affected. Administrators therefore need to understand database location, backups, recovery procedures and replication.

Do not try to open, edit, move or delete `NTDS.dit` manually. Active Directory manages this database.

## 3. What is SYSVOL?

SYSVOL is a shared folder maintained by domain controllers. It distributes important domain files to domain-joined computers.

Its default local path is commonly:

`C:\Windows\SYSVOL\sysvol`

For your domain, you should see a domain-specific folder such as:

`C:\Windows\SYSVOL\sysvol\lab.local`

SYSVOL commonly contains:

- Group Policy files: settings that administrators apply to users and computers.
- Logon and startup scripts: scripts used to automate tasks when users sign in or computers start.
- Domain policy folders: policy data associated with the domain.

### Practical company example

Suppose your company wants every domain computer to apply a password policy or display a standard configuration.

1. An administrator configures a Group Policy Object (GPO).
2. Active Directory stores information about the GPO.
3. The policy's file-based settings are stored in SYSVOL.
4. A domain-joined client retrieves and processes applicable policies.

This is why both components matter: Active Directory tracks the directory objects and policy information, while SYSVOL holds the corresponding policy files and scripts.

## 4. Hands-on Lab 1 — Verify NTDS.dit

Perform these steps inside your LAB-DC01 virtual machine, not on your personal Windows host.

### Step 1: Open PowerShell as Administrator

Open Start, search for Windows PowerShell, right-click it, and select Run as administrator.

### Step 2: Check the NTDS database location

Run:

```
Get-ItemProperty `
  "HKLM:\SYSTEM\CurrentControlSet\Services\NTDS\Parameters" |
  Select-Object "DSA Database file"
```

What the command does:

- `Get-ItemProperty` reads configuration values from the Windows Registry.
- The registry path contains Active Directory Domain Services parameters.
- `Select-Object` displays the database file location.

You should see a result similar to:

```
DSA Database file
-----------------
C:\Windows\NTDS\ntds.dit
```

The displayed path may differ in your lab.

### Step 3: Check that the file exists

```
$dbPath = (Get-ItemProperty `
  "HKLM:\SYSTEM\CurrentControlSet\Services\NTDS\Parameters")."DSA Database file"

Test-Path $dbPath
```

Expected result:

```
True
```

`True` means the file exists at the configured path. It does not, by itself, prove that the database is healthy.

## 5. Hands-on Lab 2 — Verify SYSVOL

### Step 1: Check the shared folders

Run this in PowerShell or Command Prompt:

```
net share
```

Look for these two shares:

| Share      | Purpose                                                     |
| ---------- | ----------------------------------------------------------- |
| `SYSVOL`   | Makes domain policy files and related content available     |
| `NETLOGON` | Provides access to domain logon scripts and related content |

Your earlier lab results already showed both shares, which is a good sign.

### Step 2: Check the local SYSVOL folder

Run:

```
Test-Path "C:\Windows\SYSVOL\sysvol\lab.local"
```

Expected result:

```
True
```

Then list the domain folder's contents:

```
Get-ChildItem "C:\Windows\SYSVOL\sysvol\lab.local"
```

You may see folders such as `Policies` and `scripts`. Folder contents can vary depending on the domain's configuration.

### Step 3: Check the domain shares through the network path

Run:

```
dir \\LAB-DC01\SYSVOL
```

Then:

```
dir \\LAB-DC01\NETLOGON
```

These commands test access through the network shares rather than just checking the local disk.

Important: If a command fails, don't immediately modify permissions or recreate folders. First check the error message, DNS resolution, share availability and AD DS health.

## 6. Hands-on Lab 3 — Inspect Group Policy files

Let's verify how SYSVOL relates to Group Policy.

Run:

```
Get-ChildItem "C:\Windows\SYSVOL\sysvol\lab.local\Policies"
```

If the `Policies` folder exists, list its contents. GPO folders commonly use GUIDs—unique identifiers that look like this:

`{31B2F340-016D-11D2-945F-00C04FB984F9}`

A GPO has two connected parts:

1. Group Policy Container (GPC): information about the GPO stored in Active Directory.
2. Group Policy Template (GPT): the file-based portion stored in SYSVOL.

You can inspect the policy files without changing anything:

```
Get-ChildItem `
  "C:\Windows\SYSVOL\sysvol\lab.local\Policies" `
  -Filter "gpt.ini" -Recurse
```

A `gpt.ini` file contains version information for a Group Policy template. Seeing it is useful evidence that policy files exist, but it does not guarantee every policy is configured correctly.

Don't edit these files manually during today's lab.

## 7. How are SYSVOL files kept consistent?

In a domain with multiple domain controllers, the SYSVOL content needs to stay consistent across them. Modern Windows Server domains normally use Distributed File System Replication (DFSR) for SYSVOL replication.

For example, if you later add `LAB-DC02`, you will need to understand whether SYSVOL replication is healthy between `LAB-DC01` and `LAB-DC02`.

For today's single-DC lab, there is no second domain controller to replicate with. That means you cannot yet test SYSVOL replication between domain controllers.

## 8. Common troubleshooting situations

| Symptom                          | What to check first                                                                                        |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `NTDS.dit` path cannot be read   | Confirm you are using an elevated PowerShell window and that AD DS is installed.                           |
| `SYSVOL` share is missing        | Check AD DS/DNS configuration and SYSVOL readiness; review relevant event logs.                            |
| `NETLOGON` share is missing      | Check whether the domain controller has completed initialization and advertised correctly.                 |
| `\\LAB-DC01\SYSVOL` fails        | Check hostname resolution, network connectivity, sharing and permissions.                                  |
| Group Policy is not applying     | Check client domain membership, DNS, GPO scope, permissions and Group Policy logs.                         |
| External websites do not resolve | Your isolated lab has no gateway or Internet route by design; don't confuse that with local SYSVOL health. |

You previously confirmed that `NETLOGON` and `SYSVOL` were present in `net share`. That's a positive check. We are now learning how to inspect their contents and understand their purpose.

## 9. Your Day 05 assignment

Run these commands in `LAB-DC01` and send me the outputs:

Lab checklist

0 of 5

Check NTDS.dit database path

Copy command 1

Check database file existence

Copy command 2

Check SYSVOL and NETLOGON shares

Copy command 3

Check the domain SYSVOL folder

Copy command 4

List Group Policy files

Copy command 5

If a command fails, copy the exact error message too. Don't change server configuration just to make the output match.

## 10. Interview questions

Try answering these in your own words before checking the answers.

1\. Which file is the main Active Directory database?

SYSVOL

NTDS.dit

gpt.ini

2\. Where are Group Policy template files stored?

Only inside NTDS.dit

In SYSVOL

In the Windows Recycle Bin

3\. What does the GPC store?

Group Policy information in Active Directory

Only Windows application files

The server's BIOS settings

## Day 05 PDF guide

Download the Day 05 NTDS.dit and SYSVOL Lab Guide

Once you've completed the lab, send me your command outputs. We'll review them together before moving to Day 06.