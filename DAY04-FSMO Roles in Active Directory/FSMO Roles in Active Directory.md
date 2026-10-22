# Day 04 — System Administrator Training

## Topic: FSMO Roles in Active Directory

Today we'll learn FSMO roles using your existing Windows Server 2025 VM, `LAB-DC01`.

I'll explain every concept from the basics, including why these roles exist, what each role does, practical commands, and how to verify them. You don't need prior networking or System Administration knowledge.

[Medium](https://images.openai.com/static-rsc-4/cCKYmLZfD2EUlmHG0OBVeE753VXMWkLcIb8I8r8CI-okad4S8n8VHaJAh9yjFPXTmQgtJc__QtbFipOQ140Nfy-92kRu1s2stZ88VYqUTOmU8WNz51aXgfjcYwsjkohbJNvrLes8lcLarm6hvHjvj0w7ZAMCxi3gbWutJaC910o?purpose=inline)

Your current lab

Server: `LAB-DC01`

Domain: `lab.local`

IP: `192.168.10.10`

Role: Writable Domain Controller + DNS + Global Catalog

## 1. First, understand the problem FSMO solves

Imagine your company has two Domain Controllers:

- `LAB-DC01`
- `LAB-DC02`

Both contain Active Directory information, and they replicate changes between each other.

For example, an administrator creates a user on DC01. That change can replicate to DC02.

But imagine both DCs independently try to allocate the same unique ID or make a forest-wide change at the same time. Certain operations need one designated authority to coordinate them.

That's why Active Directory uses FSMO roles.

FSMO stands for Flexible Single Master Operations.

In simple words:

> FSMO roles assign specific important Active Directory tasks to designated Domain Controllers, instead of allowing every DC to independently perform those tasks.

You have only one DC right now, so it normally holds all five FSMO roles. We'll verify that in today's lab.

## 2. The five FSMO roles

There are five roles, divided into two categories:

### Forest-wide roles — 2

1\. Schema Master

Controls changes to the Active Directory schema — the rules that define what types of information AD can store.

Example: extending the schema when certain enterprise applications require additional directory definitions.

2\. Domain Naming Master

Controls adding or removing domains and certain directory partitions in the forest.

Example: managing the addition of a new domain to an existing forest.

### Domain-wide roles — 3

3\. RID Master

Helps Domain Controllers create unique Security Identifiers (SIDs) for new users, groups and computers by allocating pools of Relative IDs (RIDs).

Example: helping ensure newly created accounts get unique security identities within the domain.

4\. PDC Emulator

Handles important password-change coordination, account lockout processing, certain Group Policy editing preferences, and the domain's primary time hierarchy role.

Example: after a password change, a DC can consult the PDC Emulator if authentication fails because it may have a more recent password.

5\. Infrastructure Master

Maintains certain cross-domain object references so that references to objects in other domains stay accurate.

Example: keeping references to users or groups from another domain updated when relevant objects change.

### Easy memory trick

- Schema Master: rules for directory objects.
- Domain Naming Master: forest domain names.
- RID Master: unique security IDs.
- PDC Emulator: passwords and time hierarchy.
- Infrastructure Master: references across domains.

Remember that FSMO roles are not five separate services that you must install. They are roles assigned to particular Domain Controllers.

## 3. Hands-on lab — Find the FSMO roles on your server

Now we'll check which server holds each role. Since you currently have one Domain Controller, all five should normally be assigned to `LAB-DC01`.

### Step 1 — Open Command Prompt as Administrator

Run:

```
netdom query fsmo
```

Expected output should list these five roles, with `LAB-DC01.lab.local` as the role holder:

```
Schema master
Domain naming master
PDC
RID pool manager
Infrastructure master
```

The exact formatting may vary slightly.

What does this command mean?

- `netdom` — a Windows domain administration utility.
- `query` — asks for information.
- `fsmo` — asks which Domain Controllers hold the FSMO roles.

This command only checks the role holders; it doesn't change your AD configuration.

### Step 2 — Verify using PowerShell

Open PowerShell as Administrator and run:

```
Get-ADForest | Select-Object SchemaMaster, DomainNamingMaster
```

This checks the two forest-wide roles.

Then run:

```
Get-ADDomain | Select-Object RIDMaster, PDCEmulator, InfrastructureMaster
```

This checks the three domain-wide roles.

If PowerShell says `Get-ADForest` or `Get-ADDomain` isn't recognized, tell me the error. We'll install or load the required Active Directory PowerShell tools if needed.

### Step 3 — Understand the result

Your expected arrangement:

Schema Master

LAB-DC01

Domain Naming Master

LAB-DC01

RID Master

LAB-DC01

PDC Emulator

LAB-DC01

Infrastructure Master

LAB-DC01

If all five show `LAB-DC01.lab.local`, your results match our expected single-DC lab.

## 4. What happens if a FSMO role holder goes offline?

This is an important real-world SysAdmin question.

Imagine you later have two DCs:

```
LAB-DC01                 LAB-DC02
192.168.10.10            192.168.10.11
     │                        │
     └──── AD replication ────┘
```

If DC01 goes offline, DC02 can still perform many normal AD operations, assuming it is healthy and reachable. But some operations that depend on a particular FSMO role may be unavailable or limited.

For example:

- PDC Emulator unavailable: password-change coordination and time-related functions can be affected.
- RID Master unavailable: DCs can continue creating objects while they have unused RIDs in their allocated pools; eventually, creating new security principals can be affected.
- Schema Master unavailable: schema changes cannot be performed normally.
- Domain Naming Master unavailable: certain forest domain-addition or removal operations cannot proceed normally.
- Infrastructure Master unavailable: certain cross-domain reference updates may be delayed.

This is why System Administrators monitor Domain Controllers and plan recovery carefully. Don't seize or transfer any FSMO role in today's lab. We only need to inspect the configuration.

## 5. Quick knowledge check

Try these questions before looking at the answers.

1\. How many FSMO roles exist in Active Directory?

3

5

7

2\. Which role coordinates RID pool allocation?

PDC Emulator

Schema Master

RID Master

3\. Which role is especially important for password-change coordination and domain time hierarchy?

PDC Emulator

Infrastructure Master

Domain Naming Master

4\. In your current single-DC lab, where should all five roles normally be?

LAB-DC01

LAB-CL01

A separate sixth server

Try again

## 6. Day 04 practical task

Run these three commands on `LAB-DC01`:

```
netdom query fsmo
```

```
Get-ADForest | Select-Object SchemaMaster, DomainNamingMaster
```

```
Get-ADDomain | Select-Object RIDMaster, PDCEmulator, InfrastructureMaster
```

Send me the outputs, and I'll verify your results.

### What you should remember today

1. FSMO means Flexible Single Master Operations.
2. Active Directory has five FSMO roles.
3. Two roles are forest-wide; three are domain-wide.
4. Your single Domain Controller normally holds all five.
5. `netdom query fsmo` is the main command to check the role holders.

Tomorrow, Day 05: the Active Directory database (`ntds.dit`) and SYSVOL — where AD stores directory data, where Group Policy files live, and how those pieces work together.