# Day 06 — Bulk Active Directory Management: CSVDE, LDIFDE & Active Directory Administrative Center

System Administrator Journey · Month 1 · Week 2: Object Lifecycle & Management

Today's roadmap topic

Your original System Administrator roadmap specifies three topics for today:

1. CSVDE — bulk import and export of Active Directory objects using CSV files.
2. LDIFDE — import and export directory information using LDIF format.
3. Active Directory Administrative Center (ADAC) — graphical management of Active Directory.

Today we'll understand all three from the basics and perform safe practical exercises in your `lab.local` domain.

## 1. Why do System Administrators need bulk management?

Imagine your company hires 100 employees.

An administrator needs to create 100 AD accounts with information such as:

- First name
- Last name
- Username
- Department
- Job title
- Office location

Would you create each user manually, one by one?

You could, but it would take time and increase the chance of mistakes.

Instead, you can prepare employee information in a spreadsheet and use bulk-management tools to import it into Active Directory.

Example: Bulk employee onboarding

Excel / CSV file

100 employee records

CSVDE

CSV import/export

LDIFDE

LDIF import/export

Active Directory — lab.local

Users and other directory objects

The important point: these tools help automate directory-data operations. They are not interchangeable with modern PowerShell automation, which we'll study on Day 07.

## 2. What is CSV?

CSV stands for Comma-Separated Values.

It is a simple text file used to store information in rows and columns.

For example, imagine you have this employee information:

| FirstName | LastName | Username | Department |
| --------- | -------- | -------- | ---------- |
| Jeevan    | Tambe    | jtambe   | IT         |
| Rahul     | Patil    | rpatil   | HR         |
| Amit      | Shinde   | ashinde  | Finance    |

A CSV file stores the same data as text:

```
FirstName,LastName,Username,Department
Jeevan,Tambe,jtambe,IT
Rahul,Patil,rpatil,HR
Amit,Shinde,ashinde,Finance
```

Each line represents a record. The first line contains the column names, also called headers.

Real-world example: HR sends the IT team an employee CSV file. The System Administrator uses it as input for account creation.

## 3. What is CSVDE?

CSVDE stands for Comma-Separated Value Directory Exchange.

It is a Windows command-line utility used to import and export Active Directory directory information in CSV format.

Two important operations:

- `-f` specifies the file.
- `-i` enables import mode.

Example:

```
csvde -f employees.csv -i
```

This attempts to import directory records from `employees.csv`.

Important limitation: CSVDE is primarily for importing and exporting directory data. It cannot set passwords during import, and it is not a general-purpose tool for modifying existing AD objects.

For creating users with passwords and more advanced account operations, PowerShell is generally more suitable.

## 4. CSVDE hands-on lab — Export Active Directory users

We'll begin with an export because it is read-only and safe.

### Step 1: Open Command Prompt as Administrator

Run this command on `LAB-DC01`:

```
csvde -f C:\AD-Users-Export.csv
```

What it means:

- `csvde` launches the CSV Directory Exchange utility.
- `-f` specifies the output file.
- `C:\AD-Users-Export.csv` is where the exported data will be saved.

### Step 2: Check the exported file

```
notepad C:\AD-Users-Export.csv
```

You should see directory records and their attributes.

The exact number of records depends on what objects exist in your domain.

### Step 3: Export only users from your domain

```
csvde -f C:\AD-Users-Only.csv -d "DC=lab,DC=local" -r "(objectCategory=person)"
```

This command specifies:

| Parameter | Meaning                            |
| --------- | ---------------------------------- |
| `-f`      | Output file                        |
| `-d`      | Starting point (search base) in AD |
| `-r`      | LDAP search filter                 |

The filter `(objectCategory=person)` can return user objects and other person-category objects, such as contacts. It is not strictly a user-only filter.

For a more specific user filter, use:

```
csvde -f C:\AD-Users-Only.csv -d "DC=lab,DC=local" -r "(&(objectCategory=person)(objectClass=user))"
```

### Step 4: Understand CSVDE import

CSVDE also supports importing records with `-i`.

```
csvde -i -f C:\AD-Import.csv
```

This reads records from the specified CSV file and attempts to add them to Active Directory.

Important: CSVDE import is not an update tool for existing objects. It is primarily used to add new directory entries. It also cannot set user passwords. Don't run an import against your domain until you've checked the file, destination OU and intended changes.

## 5. What is LDIFDE?

LDIFDE stands for LDAP Data Interchange Format Directory Exchange.

It is another Windows command-line utility for importing and exporting Active Directory information.

The difference is the file format:

- CSVDE uses comma-separated values.
- LDIFDE uses LDIF, a text format designed to represent LDAP directory entries and changes.

An LDIF record looks conceptually like this:

```
dn: CN=Jeevan Tambe,OU=Employees,DC=lab,DC=local
objectClass: user
cn: Jeevan Tambe
givenName: Jeevan
sn: Tambe
sAMAccountName: jtambe
```

Let's understand the fields:

| Field            | Meaning                                               |
| ---------------- | ----------------------------------------------------- |
| `dn`             | Distinguished Name — the object's full location in AD |
| `objectClass`    | Type of directory object                              |
| `cn`             | Common Name                                           |
| `givenName`      | First name                                            |
| `sn`             | Surname                                               |
| `sAMAccountName` | Traditional Windows logon name                        |

### Hands-on Lab — LDIFDE export

First, export directory information:

```
ldifde -f C:\AD-Export.ldf
```

Open the result:

```
notepad C:\AD-Export.ldf
```

You should see records in LDIF format.

To export only user objects:

```
ldifde -f C:\AD-Users.ldf -d "DC=lab,DC=local" -r "(&(objectCategory=person)(objectClass=user))"
```

To import an LDIF file, the general syntax is:

```
ldifde -i -f C:\AD-Import.ldf
```

LDIFDE supports more flexible directory changes than CSVDE, but you must still understand the LDIF change records and the attributes being modified before importing.

Safety rule: For today, practise exports only. We will use controlled creation and modification exercises with PowerShell in upcoming lessons.

## 6. What is Active Directory Administrative Center (ADAC)?

ADAC is a graphical tool used to manage Active Directory.

Instead of entering commands in Command Prompt, you can manage users, groups, computers, OUs and other directory objects through a graphical interface.

[How to Access Active Directory - Petri IT Knowledgebase](https://images.openai.com/static-rsc-4/MMkBuQPmphZq7r9JPZnQBvhBHRneNkG68YAWpnkol1iS_2sC26SIYmGONmm2EdAqCrG057rdEGkcHYZg_zbAvYpC4_DY6IQCCTcTPYx5TuM3VRxc6a3T-daeDU5s2VBxL3gDMXiGpxv4b1JyyHV4IV9fyZKwxnDbyYIXPyZWx4o?purpose=inline)

[New features in Active Directory Domain Services in Windows Server 2012, Part 5: PowerShell History Viewer   - The things that are better left unspoken](https://images.openai.com/static-rsc-4/0jjdcMWC20Ml6BUYY1YaXQydtfjnXynsx1y9kCduS8slybxGofwtHcxChWuaAWp4DEwqLRCmO9-MP6OR718qazT-g0ZCR7hozkiP6QLTfuLj9YSJSNVmVrYy2LSP-Ij117Yxp1_TRTg9zHGM0054_q8fqCCHKeIeVYhOYirS3dQ?purpose=inline)

[Active Directory Administrative Center - Active Directory Pro](https://images.openai.com/static-rsc-4/v2fMSr20yIwRDHTycOnKNSC_Yrq-2BG5B0gJsO34GOcArYY7A4UbABtRfk0oDgSE695-ecsD_4M5TL63WI35D1TeEID4AWoUOyEfQD1wgUgLGUPvFRp_Ls6g7gASorvaaXRqnLRM-3CnMc906yLOXr2d6GP0WZ2ly0waDvnuwYg?purpose=inline)

7

### How to open ADAC

On `LAB-DC01`:

1. Open the Start menu.
2. Search for Active Directory Administrative Center.
3. Open it.
4. Select your domain, `lab.local`.

You can also open it using:

```
dsac.exe
```

### What can you do in ADAC?

- Create and manage users.
- Create and manage groups.
- Create and manage Organizational Units.
- Search for directory objects.
- Manage user properties.
- Use the Active Directory Recycle Bin, if enabled.

### ADAC vs ADUC

You have already used Active Directory Users and Computers (ADUC), so let's compare them.

| Feature                 | ADUC                                 | ADAC                                   |
| ----------------------- | ------------------------------------ | -------------------------------------- |
| Full name               | Active Directory Users and Computers | Active Directory Administrative Center |
| Interface               | Traditional MMC console              | Modern graphical interface             |
| User management         | Yes                                  | Yes                                    |
| Group management        | Yes                                  | Yes                                    |
| OU management           | Yes                                  | Yes                                    |
| Recycle Bin integration | Limited                              | Integrated                             |
| PowerShell integration  | Limited                              | Better integration                     |

Both are management tools. Neither automatically gives you administrator permissions. Your account still needs the appropriate delegated permissions.

## 7. CSVDE vs LDIFDE vs ADAC

This comparison is important for interviews.

| Tool   | Type         | Main purpose                           |
| ------ | ------------ | -------------------------------------- |
| CSVDE  | Command line | Import/export directory data in CSV    |
| LDIFDE | Command line | Import/export directory data in LDIF   |
| ADAC   | GUI          | Manage directory objects interactively |

Real company example:

- HR provides a spreadsheet of 200 new employees.
- The administrator prepares and validates the directory data.
- CSVDE or LDIFDE can be used for suitable bulk directory imports.
- ADAC can be used to inspect or manage individual objects.
- PowerShell can automate more advanced onboarding workflows.

## 8. Your practical assignment on LAB-DC01

Safe practice — export and inspect only

Run these commands one by one.

Task 1 — CSVDE full export

```
csvde -f C:\AD-Users-Export.csv
```

Task 2 — CSVDE user export

```
csvde -f C:\AD-Users-Only.csv -d "DC=lab,DC=local" -r "(&(objectCategory=person)(objectClass=user))"
```

Task 3 — LDIFDE full export

```
ldifde -f C:\AD-Export.ldf
```

Task 4 — LDIFDE user export

```
ldifde -f C:\AD-Users.ldf -d "DC=lab,DC=local" -r "(&(objectCategory=person)(objectClass=user))"
```

Task 5 — Open ADAC

```
dsac.exe
```

Check that you can open your `lab.local` domain and browse its objects.

Do not import or delete anything today. Export files can contain sensitive directory information, so keep them inside your private lab and don't upload them publicly.

## 9. Interview questions

Try these before looking up the answers.

1\. Which tool uses CSV format?

LDIFDE

CSVDE

ADAC



2\. Which tool uses LDIF format?

CSVDE

ADAC

LDIFDE



3\. Which tool provides a graphical interface for AD management?

ADAC

CSVDE

LDIFDE



4\. Can CSVDE set a user's password during import?

Yes

No



5\. Does opening ADAC automatically grant Domain Admin permissions?

Yes

No



Answered: 0/5

## 10. Day 06 completion checklist

Your progress

## 0/6

CSVDE export completed

CSVDE user-filter export completed

LDIFDE export completed

LDIFDE user-filter export completed

ADAC opened successfully

Reviewed the exported files

Your Day 06 PDF guide:

Download System Administrator Day 06 — CSVDE, LDIFDE & ADAC

What comes next — Day 07: PowerShell basics for Active Directory:

- `Get-ADUser`
- `New-ADUser`
- `Disable-ADAccount`

This is where we'll start managing AD users with PowerShell, an essential skill for a System Administrator.

Complete today's export exercises and share your outputs before moving ahead.

Roadmap topic follows your original Sys Admin study plan.&#x20;

Sys Admin.pdf
