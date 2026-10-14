# 🟢 Day 3 — Domain Controllers, Global Catalog & RODC

Today we continue the **3-month System Administrator journey**. According to your roadmap, Day 3 is specifically about **Domain Controllers, Global Catalogs, and Writable vs. Read-Only Domain Controllers (RODCs)**. 

Since you're learning from **zero**, I will first explain the words in very simple language, then connect them to your `LAB-DC01` and finally do a hands-on lab.

---

# 🎯 Today's Goal

By the end of today, you should understand:

```text
Domain
   ↓
Domain Controller (DC)
   ↓
Global Catalog (GC)
   ↓
Writable DC
   ↓
Read-Only DC (RODC)
```

And you should be able to answer:

> **"What exactly is a Domain Controller, why do we need it, and what is the difference between a DC, GC and RODC?"**

---

# ⏱️ Day 3 — 2 Hour Plan

| Time      | Activity                        |
| --------- | ------------------------------- |
| 30–40 min | 🧠 Concepts                     |
| 45–60 min | 💻 Hands-on LAB-DC01            |
| 15 min    | 🔍 Troubleshooting/verification |
| 15 min    | 🎯 Interview questions          |

---

# Part 1 — First understand "Domain"

You created:

```text
lab.local
```

This is your **AD domain**.

Think about a company.

```text
Company: ABC Pvt Ltd

Domain:
abc.local
```

Employees might have:

```text
Rahul
Priya
Jeevan
Admin
```

Computers:

```text
HR-PC01
FIN-PC01
CEO-PC01
LAPTOP01
```

Instead of every computer independently managing all these users, the company has a central system.

That central system is **Active Directory**.

---

# Part 2 — What is a Domain Controller?

This is today's most important concept.

### Simple definition:

> **A Domain Controller is a server that runs Active Directory Domain Services (AD DS) and manages authentication and directory information for the domain.**

Your server:

```text
LAB-DC01
     │
     ├── Active Directory
     ├── DNS
     ├── Authentication
     ├── Users
     ├── Computers
     ├── Groups
     └── Policies
```

That's why your server is called:

```text
LAB-DC01
```

**DC = Domain Controller**

---

# 🧠 Simple real-world example

Imagine your company has 1,000 employees.

Jeevan wants to log in to:

```text
PC-001
```

He enters:

```text
Username: jeevan
Password: ********
```

The PC asks the Domain Controller:

```text
PC-001
   │
   │ "Is Jeevan's password correct?"
   ▼
LAB-DC01
   │
   │ Check Active Directory
   ▼
User: Jeevan
Password: ✔
Account: Enabled ✔
   │
   ▼
Login allowed
```

So the DC acts like the **central identity authority**.

Your roadmap describes AD similarly as a centralized directory/security system hosted on a Domain Controller. 

---

# Part 3 — Is the DC just a normal server?

No.

A normal Windows Server might provide:

```text
File sharing
Printing
Applications
Web server
Database
```

A Domain Controller has a special role:

```text
AD DS
Authentication
Directory
Kerberos
LDAP
DNS integration
Group Policy
```

Your `LAB-DC01` became a DC when you promoted the server to the domain controller role.

You already verified this with:

```cmd
dcdiag
```

and got:

```text
Advertising       PASS
SysVolCheck       PASS
NetLogons         PASS
Replications      PASS
Services          PASS
```

That is a very good sign.

---

# Part 4 — What is a Writable Domain Controller?

This is the **normal type of Domain Controller**.

"Writable" means:

> You can make changes to Active Directory on that DC.

For example:

```text
Create user
     ↓
LAB-DC01
     ↓
AD database changed
```

Or:

```text
Disable user
Change password
Create group
Delete computer
Modify group membership
```

These changes can be written to the AD database.

Your:

```text
LAB-DC01
```

is a **Writable DC**.

---

# Part 5 — What is a Global Catalog?

This sounds complicated, but the basic idea is simple.

A **Global Catalog (GC)** helps users and applications **find objects across the Active Directory forest**.

Imagine a large company:

```text
Forest
│
├── india.local
│
├── uk.india.local
│
└── usa.india.local
```

There could be thousands of users.

A user might search:

```text
"Find Jeevan"
```

The Global Catalog helps answer:

> "Where is this object in the forest?"

### Think of GC as:

**AD's searchable directory/index for the forest.**

---

# 🧠 Important distinction

Don't think:

> "GC is another separate server."

A GC is actually a **Domain Controller that has been configured to hold the Global Catalog**.

So:

```text
Domain Controller
       │
       ├── Can be Writable
       │
       └── Can also be Global Catalog
```

Therefore one server can be:

```text
LAB-DC01
   │
   ├── Domain Controller ✅
   ├── Writable DC ✅
   ├── Global Catalog ✅
   └── DNS Server ✅
```

Your original DC promotion configuration specifically enabled **DNS Server and Global Catalog**. 

---

# Part 6 — What is RODC?

RODC =

**Read-Only Domain Controller**

Let's break the name:

### Read

It can **read** AD information.

### Only

It cannot normally accept ordinary direct changes to the AD database.

Therefore:

```text
Writable DC
     ↓
Can read + write

RODC
     ↓
Primarily read-only
```

---

# 🏢 Why would a company use an RODC?

Imagine your company has:

```text
Head Office
Mumbai
```

and a small branch:

```text
Branch Office
Remote location
```

The branch might have poor physical security.

You don't necessarily want a fully writable DC sitting there.

Instead:

```text
              HEAD OFFICE
             ┌───────────┐
             │ Writable  │
             │ DC        │
             └─────┬─────┘
                   │
                Network
                   │
             ┌─────▼─────┐
             │   RODC    │
             │ Branch    │
             └───────────┘
```

If someone physically steals the branch server, having a writable copy of the entire directory there can be a bigger security concern.

RODC provides a more controlled model.

---

# ⭐ Very important comparison

| Feature                   | Writable DC        | RODC                              |
| ------------------------- | ------------------ | --------------------------------- |
| Read AD                   | ✅                  | ✅                                 |
| Write AD                  | ✅                  | ❌                                 |
| Authentication            | ✅                  | ✅                                 |
| Global Catalog            | Can be             | Can be                            |
| Useful for branch offices | Yes                | ⭐ Very useful                     |
| Security exposure         | Higher             | Lower                             |
| AD changes                | Can originate here | Normally forwarded to writable DC |

Don't memorize everything yet.

Just remember:

```text
Writable DC = Read + Write

RODC = Read
```

---

# Part 7 — Your LAB Architecture

Currently you have:

```text
Windows 11 Host
     │
     │ Hyper-V
     ▼
 LabNetwork
     │
     ▼
┌──────────────────────────┐
│       LAB-DC01           │
│                          │
│ IP: 192.168.10.10        │
│ Domain: lab.local        │
│                          │
│ AD DS       ✅           │
│ DNS         ✅           │
│ Global      ✅           │
│ Catalog                  │
│ Writable DC ✅           │
└──────────────────────────┘
```

Later we'll add:

```text
                 LAB-DC01
              192.168.10.10
                    │
          ┌─────────┼──────────┐
          │         │          │
          ▼         ▼          ▼
      LAB-CL01   LAB-SRV01   LAB-DC02
      Client     Server      2nd DC
```

That's when these concepts become much more practical.

---

# 💻 Part 2 — Hands-on Lab

Now let's **prove** what we learned.

## Step 1 — Check Domain Controller status

Open CMD as Administrator.

Run:

```cmd
hostname
```

Expected:

```text
LAB-DC01
```

Then:

```cmd
echo %USERDOMAIN%
```

Expected:

```text
LAB
```

Then:

```cmd
whoami
```

You should see something similar to:

```text
lab\administrator
```

---

# Step 2 — Verify AD DS service

Run:

```cmd
sc query ntds
```

Look for:

```text
STATE : 4  RUNNING
```

### What is NTDS?

NTDS stands for **NT Directory Services**.

It is the core Active Directory service.

For now remember:

```text
NTDS
 ↓
Active Directory service
```

---

# Step 3 — Verify Global Catalog

Open:

```cmd
dsa.msc
```

This opens:

**Active Directory Users and Computers**

Then:

```text
lab.local
   ↓
Domain Controllers
   ↓
LAB-DC01
```

Right-click:

**LAB-DC01 → Properties**

Look for the **Global Catalog** option.

It should be checked because you selected GC during DC promotion.

---

# Step 4 — Use Active Directory Sites and Services

Press:

```text
Win + R
```

Type:

```text
dssite.msc
```

Press Enter.

You should see something similar to:

```text
Sites
└── Default-First-Site-Name
    ├── Servers
    │   └── LAB-DC01
    │       └── NTDS Settings
```

Don't change anything yet.

We're only exploring.

---

# Step 5 — Understand what you are seeing

You now have:

```text
Sites
  │
  └── Default-First-Site-Name
          │
          └── Servers
                │
                └── LAB-DC01
                      │
                      └── NTDS Settings
```

This becomes **very important later** when we study:

* AD Sites
* Subnets
* Replication
* KCC
* Multi-DC environments

Those topics are later in your roadmap. 

---

# Step 6 — Verify Global Catalog using PowerShell

Open PowerShell as Administrator.

Run:

```powershell
Get-ADDomainController -Identity "LAB-DC01" | Select-Object Name,IsGlobalCatalog,IsReadOnly
```

You should get approximately:

```text
Name      IsGlobalCatalog   IsReadOnly
----      ---------------   ----------
LAB-DC01  True              False
```

### This is excellent for your SysAdmin knowledge.

It tells us:

```text
IsGlobalCatalog = True
```

➡️ LAB-DC01 is a GC.

And:

```text
IsReadOnly = False
```

➡️ LAB-DC01 is a writable DC.

Therefore:

> **LAB-DC01 is a writable Domain Controller and Global Catalog server.**

---

# 🧪 Step 7 — One very important test

Run:

```powershell
Get-ADDomainController -Filter * | Format-Table Name,IPv4Address,IsGlobalCatalog,IsReadOnly
```

You currently have only one DC, so you should see:

```text
LAB-DC01
192.168.10.10
True
False
```

Later, when we create:

```text
LAB-DC02
```

you'll be able to compare two DCs.

---

# 🧠 Networking foundation — DON'T skip this

Because you told me you are starting with **zero networking knowledge**, I want you to understand one tiny but very important point today.

You have:

```text
LAB-DC01
192.168.10.10
```

What is `192.168.10.10`?

It is an **IP address**.

Think of it as the **address of your server on the network**.

Your future client might be:

```text
LAB-CL01
192.168.10.30
```

Then:

```text
LAB-DC01                 LAB-CL01
192.168.10.10            192.168.10.30
      │                        │
      └──────── Network ───────┘
```

The client needs to know:

> "Where is my Domain Controller?"

That's where **DNS** becomes extremely important.

Instead of remembering:

```text
192.168.10.10
```

the client can use:

```text
LAB-DC01.lab.local
```

And DNS translates the name to the IP address.

```text
LAB-DC01.lab.local
        ↓
       DNS
        ↓
192.168.10.10
```

This is one of the reasons we carefully configured the DC's networking and DNS before adding clients.

---

# 🎯 Day 3 Mini Task

Don't create `LAB-CL01` yet.

First run these commands on `LAB-DC01`:

### CMD

```cmd
hostname
echo %USERDOMAIN%
whoami
sc query ntds
```

### PowerShell

```powershell
Get-ADDomainController -Identity "LAB-DC01" | Select-Object Name,IsGlobalCatalog,IsReadOnly
```

and:

```powershell
Get-ADDomainController -Filter * | Format-Table Name,IPv4Address,IsGlobalCatalog,IsReadOnly
```

---

# 🎤 Day 3 Interview Questions

Try answering these **without looking above**:

### Q1. What is a Domain Controller?

### Q2. What is the difference between a normal Windows Server and a Domain Controller?

### Q3. What is a Global Catalog?

### Q4. Can a Domain Controller also be a Global Catalog?

### Q5. What is a writable DC?

### Q6. What does RODC stand for?

### Q7. What is the basic difference between a Writable DC and RODC?

### Q8. Why might a company use an RODC in a branch office?

### Q9. Is `LAB-DC01` currently writable?

### Q10. Is `LAB-DC01` currently a Global Catalog?

---

# 📌 Day 3 — What you should remember

Don't try to memorize 50 things today. Remember these **6**:

```text
1. Domain Controller
   = Server running AD DS

2. Writable DC
   = Can read + write AD

3. RODC
   = Read-only Domain Controller

4. Global Catalog
   = Helps search/find objects across the forest

5. One DC can be both:
   Writable DC + Global Catalog

6. LAB-DC01
   = Writable DC + Global Catalog + DNS
```

Your roadmap's next topic after today's DC/GC/RODC lesson is **Day 4: FSMO Roles — RID, PDC, Infrastructure, Schema Master and Domain Naming Master**. 

**Complete the commands above and send me the output. I will check your LAB-DC01 first, explain anything you don't understand, and then we'll consider Day 3 complete.**
