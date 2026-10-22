# Day 04 — Active Directory FSMO Roles

System Administrator Journey · Month 1 · Core Active Directory · Beginner to hands-on

Today we're learning FSMO roles. This is the next topic in your 60-day SysAdmin roadmap after Domain Controllers, Global Catalogs, and RODCs.

Sys Admin.pdf

Don't worry if the name looks complicated. We'll start from the basics, use your existing `LAB-DC01`, and explain what every command does.

## Today's learning goals

By the end of this lesson, you should be able to:

* Explain what FSMO means and why these roles exist.

* Understand the five FSMO roles in simple language.

* Check which server holds each role in your lab.

* Explain what happens if a role holder goes offline.

* Answer common SysAdmin interview questions.

## 1. First, understand the problem FSMO solves

Imagine your company has two Domain Controllers:

LAB-DC01

192.168.10.10

Active Directory replication

LAB-DC02

A second Domain Controller we'll create later

Both servers can normally accept many Active Directory changes. For example, an administrator can create a user on one writable DC, and that change can replicate to the other DC.

But some operations need one clearly designated authority to coordinate them. If every DC independently performed certain operations, conflicts or inconsistencies could occur.

Microsoft uses special roles called FSMO roles for those operations.

FSMO stands for Flexible Single Master Operations.

In simple words:

> FSMO roles assign specific Active Directory responsibilities to designated Domain Controllers.

A server holding one of these responsibilities is called a role holder.

## 2. The five FSMO roles

There are five FSMO roles. Two operate at the forest level, and three operate at the domain level.

## Forest-level roles — 2

1. Schema Master

Think of the schema as the blueprint for the types of information AD can store.

Example: when an application needs new AD schema definitions, schema changes are coordinated through the Schema Master.

Remember: Schema = blueprint.

2. Domain Naming Master

Controls the addition and removal of domains in the forest.

Example: if a company adds another domain to its existing forest, this role helps ensure domain names are managed consistently.

Remember: Domain Naming = adding/removing domains.

## Domain-level roles — 3

3. RID Master

RID means Relative Identifier. AD uses identifiers to give security principals, such as users and groups, unique security identifiers (SIDs).

The RID Master allocates pools of RIDs to DCs so they can create security principals without assigning duplicate RIDs.

Remember: RID = identifier allocation.

4. PDC Emulator

This role has several important responsibilities involving password-change handling, account lockout processing, and time synchronization.

Example: when a user changes their password, the PDC Emulator helps other DCs handle authentication attempts involving recent password changes.

Remember: PDC = passwords, time, and important domain coordination.

5. Infrastructure Master

Helps maintain references to objects from other domains, such as references to users or groups belonging to another domain.

This matters most in forests containing multiple domains.

Remember: Infrastructure = cross-domain references.

### Easy way to memorize

| Scope  | FSMO role             | Main responsibility            |
| ------ | --------------------- | ------------------------------ |
| Forest | Schema Master         | Schema changes                 |
| Forest | Domain Naming Master  | Add/remove domains             |
| Domain | RID Master            | Allocate RID pools             |
| Domain | PDC Emulator          | Password coordination and time |
| Domain | Infrastructure Master | Cross-domain references        |

For your current single-domain forest, `lab.local`, all five roles will normally be held by `LAB-DC01`.

## 3. Hands-on lab — discover your FSMO role holders

Now let's check your actual server rather than just memorizing theory.

### Step 1 — Open PowerShell as Administrator

On your `LAB-DC01` VM, open Windows PowerShell.

First, check whether the Active Directory module is available:

PowerShell

```
Get-Module -ListAvailable ActiveDirectory
```

If it appears in the output, load it:

PowerShell

```
Import-Module ActiveDirectory
```

What does this mean?

* `Get-Module` checks PowerShell modules.

* `-ListAvailable` lists modules installed on the machine.

* `Import-Module` loads the module so its commands can be used.

### Step 2 — Find the five role holders

Run:

PowerShell

```
netdom query fsmo
```

Expected output should look similar to:

```
Schema master               LAB-DC01.lab.local
Domain naming master        LAB-DC01.lab.local
PDC                         LAB-DC01.lab.local
RID pool manager            LAB-DC01.lab.local
Infrastructure master       LAB-DC01.lab.local
```

The exact formatting may differ slightly.

What does this prove?

It shows which DC currently holds each FSMO role. If all five list `LAB-DC01.lab.local`, that is expected for your current lab.

### Step 3 — Check using PowerShell

Run:

PowerShell

```
Get-ADForest | Select-Object SchemaMaster, DomainNamingMaster
```

This checks the two forest-level role holders.

Then:

PowerShell

```
Get-ADDomain | Select-Object PDCEmulator, RIDMaster, InfrastructureMaster
```

This checks the three domain-level role holders.

You now have two ways to investigate FSMO roles: `netdom` and PowerShell.

## 4. What if a role holder goes offline?

Imagine `LAB-DC01` is your only DC and it shuts down.

Your lab's domain services become unavailable while it is offline because there is no second DC to provide them.

In a real company with two DCs, the impact depends on which role is affected and whether the other DCs and services remain available.

For example:

* RID Master unavailable: existing users may still authenticate, but RID allocation can eventually become a problem when DCs exhaust their available RID pools.

* PDC Emulator unavailable: password-change handling and time synchronization can be affected.

* Schema Master unavailable: ordinary AD activity can continue, but schema changes cannot proceed normally.

* Domain Naming Master unavailable: normal activity can continue, but domain additions/removals are affected.

* Infrastructure Master unavailable: some cross-domain reference updates can be affected.

Important: A failed FSMO role holder does not automatically mean the whole domain immediately stops working.

Later, when we add `LAB-DC02`, we'll practise how multiple DCs improve resilience and learn the difference between transferring a role gracefully and seizing a role after a failure.

## 5. Interview practice

Try answering these before looking at the answers.

1. What does FSMO stand for?

Flexible Single Master Operations.

Hide answer

2. How many FSMO roles are there?

Five: Schema Master, Domain Naming Master, RID Master, PDC Emulator, and Infrastructure Master.

Hide answer

3. Which two roles are forest-level?

Schema Master and Domain Naming Master.

Hide answer

4. Which role allocates RID pools?

RID Master.

Hide answer

5. Which role is especially important for domain time synchronization and password-change handling?

PDC Emulator.

Hide answer

6. Does losing one FSMO role immediately stop all domain authentication?

No. The impact depends on the role and environment. Many ordinary AD operations can continue.

Hide answer

## 6. Your Day 04 task

Complete these three commands on `LAB-DC01`:

PowerShell

```
netdom query fsmo
```

PowerShell

```
Get-ADForest | Select-Object SchemaMaster, DomainNamingMaster
```

PowerShell

```
Get-ADDomain | Select-Object PDCEmulator, RIDMaster, InfrastructureMaster
```

Send me the output, and I'll verify your results.

### Day 04 notes for your GitHub repository

Suggested folder

`Day-04-FSMO-Roles`

Copy

Suggested files

`README.md` — FSMO concepts and the five roles

`Commands.md` — Commands and expected results

`Screenshots/` — Your actual lab screenshots

Today's key takeaway: FSMO roles assign five specific responsibilities to designated DCs. Your `LAB-DC01` should currently hold all five.
