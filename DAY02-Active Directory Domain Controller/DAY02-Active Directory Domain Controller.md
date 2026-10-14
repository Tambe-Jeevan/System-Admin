Excellent. **LAB-DC01 is now successfully installed** with Windows Server 2025 Standard Evaluation, 4 GB RAM, and a 100 GB VHDX. That's enough for our first DC lab.

One thing I notice: the VM currently has **1 virtual processor / 2 logical processors**. That's acceptable for this lab, but because your host has 32 GB RAM, we'll later use **2 vCPUs** where appropriate.

# 🟢 DAY 2 — Active Directory Domain Controller

Today we start from the absolute basics.

Your goal by the end of Day 2:

```text
Windows Server 2025
       ↓
Static IP
       ↓
Computer Name = LAB-DC01
       ↓
Install AD DS
       ↓
Install DNS
       ↓
Create Forest
       ↓
Domain = lab.local
       ↓
LAB-DC01 becomes Domain Controller
       ↓
Verify AD Users & Computers
```

This follows the structure of the original 60-day plan, where the first phase focuses on AD architecture and identity fundamentals. 

---

# 🧠 Part 1 — First understand what we're doing

Before clicking anything, understand these 5 terms.

### 1. Active Directory

**Active Directory Domain Services (AD DS)** is Microsoft's directory service used to centrally manage:

* Users
* Computers
* Groups
* Policies
* Authentication
* Permissions

Think:

```text
Company
   │
   └── Active Directory
          │
          ├── Users
          ├── Computers
          ├── Groups
          └── Policies
```

---

### 2. Domain Controller

A **Domain Controller (DC)** is a Windows Server that runs AD DS.

Our server:

```text
LAB-DC01
```

will eventually become:

```text
LAB-DC01
   │
   ├── Active Directory
   ├── DNS
   ├── Global Catalog
   └── Authentication
```

---

### 3. Domain

Our practice domain will be:

```text
lab.local
```

For example:

```text
Administrator@lab.local
```

Later we will create:

```text
jeevan@lab.local
rahul@lab.local
admin@lab.local
```

---

### 4. DNS

This is **extremely important**.

Active Directory depends heavily on DNS.

Our DC will eventually provide DNS for the lab:

```text
LAB-DC01
192.168.10.10
     │
     └── DNS
          │
          └── lab.local
```

That's why we need to configure networking correctly **before** promoting the server.

Your original guide also specifies a static IP and the DC pointing its DNS to itself. 

---

### 5. Forest

A **forest** is the highest-level AD logical structure.

For our first lab:

```text
Forest
└── lab.local
```

Don't worry about trees, trusts and multiple forests yet. We'll build those concepts gradually.

---

# 🟡 STEP 1 — Check your current server name

On LAB-DC01:

Press:

```text
Win + R
```

Type:

```text
sysdm.cpl
```

Press Enter.

Check:

```text
Computer name:
LAB-DC01
```

If it already says **LAB-DC01**, don't change it.

Your supplied system information confirms the VM is already named **LAB-DC01**, so this step should already be complete.

---

# 🟡 STEP 2 — Configure Static IP

This is today's **most important configuration before AD promotion**.

Open:

```text
Win + R
```

Enter:

```text
ncpa.cpl
```

Press Enter.

You should see the VM's network adapter.

Right-click:

**Ethernet → Properties**

Select:

**Internet Protocol Version 4 (TCP/IPv4)**

→ **Properties**

Enter:

```text
IP address:       192.168.10.10

Subnet mask:      255.255.255.0

Default gateway:  [leave blank]

Preferred DNS:    127.0.0.1

Alternate DNS:    [leave blank]
```

So:

```text
┌────────────────────────────────────┐
│ Internet Protocol Version 4        │
├────────────────────────────────────┤
│ IP address                         │
│ 192.168.10.10                      │
│                                    │
│ Subnet mask                        │
│ 255.255.255.0                      │
│                                    │
│ Default gateway                    │
│                                    │
│ Preferred DNS                      │
│ 127.0.0.1                          │
│                                    │
│ Alternate DNS                      │
│                                    │
└────────────────────────────────────┘
```

The original lab instructions use this same lab address and configuration. 

Click:

**OK → Close**

---

# 🧪 STEP 3 — Test the network

Open **Command Prompt as Administrator**.

Run:

```cmd
ipconfig
```

You should see approximately:

```text
Ethernet adapter Ethernet:

   IPv4 Address. . . . . . : 192.168.10.10
   Subnet Mask . . . . . . : 255.255.255.0
```

Then:

```cmd
ping 127.0.0.1
```

Expected:

```text
Reply from 127.0.0.1
```

Then:

```cmd
ping 192.168.10.10
```

Expected:

```text
Reply from 192.168.10.10
```

### Don't worry if:

```cmd
ping 8.8.8.8
```

doesn't work.

Our **Internal Hyper-V network is intentionally isolated**. Internet access isn't required for the core AD lab.

---

# 🟡 STEP 4 — Check the server's time

Run:

```cmd
date /t
time /t
```

Also check:

```cmd
tzutil /g
```

For your lab, we want the correct India timezone:

```text
India Standard Time
```

If it isn't correct, tell me before continuing.

### Why does time matter?

Because Active Directory authentication relies heavily on Kerberos, and Kerberos is sensitive to clock differences.

This becomes an important troubleshooting topic later.

---

# 🟡 STEP 5 — Install Active Directory Domain Services

Open:

**Server Manager**

Select:

**Manage → Add Roles and Features**

You'll get the wizard.

### Screen 1

**Before you begin**

Click:

**Next**

---

### Screen 2

Select:

**Role-based or feature-based installation**

Click:

**Next**

---

### Screen 3

You'll see:

```text
Select destination server
```

Make sure:

```text
LAB-DC01
```

is selected.

Click:

**Next**

---

# 🟡 STEP 6 — Select AD DS

At:

**Server Roles**

find:

```text
☐ Active Directory Domain Services
```

Check it.

A popup appears:

```text
Add Roles and Features?
```

Click:

**Add Features**

Then:

**Next**

The original Day 1 guide follows this same AD DS installation flow. 

---

# 🟡 STEP 7 — Features

Don't randomly select features.

Click:

**Next**

---

# 🟡 STEP 8 — AD DS information

You'll see information about Active Directory Domain Services.

Read it.

Then:

**Next**

---

# 🟡 STEP 9 — Installation

Click:

**Install**

Wait until you see:

```text
Installation succeeded
```

**Do not reboot manually yet if the wizard has not completed.**

---

# 🧠 Important concept

At this point:

```text
LAB-DC01
   │
   └── AD DS ROLE INSTALLED
```

But it is **NOT YET a Domain Controller**.

This distinction is important for interviews.

### Installing AD DS ≠ Domain Controller

We still need to perform:

```text
AD DS installation
        ↓
Domain Controller promotion
```

---

# 🟢 STEP 10 — Promote LAB-DC01

After installation finishes, look at the top-right of Server Manager.

You'll see a notification flag.

Click it.

You should see something similar to:

```text
Configuration required for Active Directory Domain Services
```

Select:

**Promote this server to a domain controller**

---

# 🟢 STEP 11 — Deployment Configuration

You'll see:

```text
Select the deployment operation
```

Because this is our first DC:

Select:

### ✅ Add a new forest

Then enter:

```text
Root domain name:

lab.local
```

So:

```text
Deployment Configuration

○ Add a domain controller to an existing domain
● Add a new forest

Root domain name:
lab.local
```

Click:

**Next**

The original roadmap uses **Add a new forest → lab.local** for this first DC. 

---

# 🟢 STEP 12 — Domain Controller Options

You'll see:

### Forest functional level

Leave the default.

### Domain functional level

Leave the default.

### Domain Controller capabilities

Make sure:

```text
☑ Domain Name System (DNS) server
☑ Global Catalog (GC)
```

Leave:

```text
☐ Read only domain controller (RODC)
```

---

## DSRM Password

You'll see:

**Directory Services Restore Mode (DSRM) password**

Create a strong lab password.

For example, use your own unique lab password rather than copying one from an example.

### ⚠️ Remember it

This password is important for future backup/recovery practice.

We're going to use DSRM again in Month 2 when we study AD disaster recovery.

Your roadmap explicitly includes DSRM boot/recovery as a later topic. 

Click:

**Next**

---

# 🟢 STEP 13 — DNS Options

You'll probably see a warning about:

```text
DNS delegation
```

For this isolated lab:

**Ignore the delegation warning.**

Click:

**Next**

This is also what your original setup guide specifies. 

---

# 🟢 STEP 14 — Additional Options

You should see:

```text
NetBIOS domain name:
LAB
```

This comes from:

```text
lab.local
```

So:

```text
DNS Domain:
lab.local

NetBIOS:
LAB
```

Click:

**Next**

---

# 🟢 STEP 15 — AD database paths

You'll see:

```text
Database folder:
C:\Windows\NTDS

Log files folder:
C:\Windows\NTDS

SYSVOL folder:
C:\Windows\SYSVOL
```

### For our first lab:

**Leave them at the defaults.**

Later, when we learn enterprise storage architecture, we'll discuss why administrators may separate AD database, logs and SYSVOL onto different disks.

The original guide also recommends leaving these paths at their defaults for the initial lab. 

Click:

**Next**

---

# 🟢 STEP 16 — Review

Review:

```text
New forest
lab.local
LAB
DNS
Global Catalog
```

Click:

**Next**

Windows will perform prerequisite checks.

You want:

```text
All prerequisite checks passed successfully
```

Some warnings may be informational rather than fatal.

**Don't install yet if you see a red error.**

If you see a red error, take a screenshot and send it to me.

---

# 🟢 STEP 17 — Install

If the prerequisite checks pass:

Click:

**Install**

The server will automatically restart.

This is expected.

---

# 🟢 STEP 18 — Login after reboot

After reboot, the login screen should change.

Before promotion you had a local server account.

After promotion you'll see the domain context.

Something like:

```text
LAB\Administrator
```

or:

```text
Administrator@lab.local
```

Log in.

---

# 🧪 STEP 19 — Verify Domain Controller

Open **Command Prompt as Administrator**.

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
ipconfig
```

Confirm:

```text
192.168.10.10
```

Then:

```cmd
whoami
```

You should see the domain context.

---

# 🧪 STEP 20 — Verify Active Directory

Press:

```text
Win + R
```

Run:

```text
dsa.msc
```

This opens:

# Active Directory Users and Computers

You should see:

```text
Active Directory Users and Computers

└── lab.local
    ├── Builtin
    ├── Computers
    ├── Domain Controllers
    ├── ForeignSecurityPrincipals
    ├── Infrastructure
    ├── LostAndFound
    ├── NTDS Quotas
    ├── Program Data
    ├── System
    └── Users
```

Your original Day 1 goal was to reach ADUC and verify the new `lab.local` environment. 

---

# 🧪 STEP 21 — Verify DNS

Run:

```text
dnsmgmt.msc
```

You should see:

```text
DNS
└── LAB-DC01
    ├── Forward Lookup Zones
    │   └── lab.local
    │
    └── Reverse Lookup Zones
```

We'll properly configure and troubleshoot DNS during the upcoming days.

---

# 🧪 STEP 22 — Run your first DC diagnostic

Open Administrator CMD:

```cmd
dcdiag
```

Don't worry if you don't understand the output yet.

**Save the output.**

We'll learn how to read it.

Also run:

```cmd
net share
```

You should eventually see important AD shares such as:

```text
NETLOGON
SYSVOL
```

These are extremely important.

---

# 🎯 DAY 2 SUCCESS CHECKLIST

Don't consider Day 2 complete until these work:

```text
☐ LAB-DC01 installed
☐ Static IP = 192.168.10.10
☐ Computer name = LAB-DC01
☐ AD DS installed
☐ New forest created
☐ Domain = lab.local
☐ DNS installed
☐ Global Catalog enabled
☐ DC rebooted
☐ LAB\Administrator login works
☐ dsa.msc opens
☐ lab.local appears
☐ Domain Controllers container appears
☐ DNS Manager opens
☐ dcdiag runs
☐ SYSVOL exists
☐ NETLOGON exists
```

---

# 📚 DAY 2 THEORY — Learn these before Day 3

You should be able to answer these **without looking at notes**:

### Q1. What is Active Directory?

### Q2. What is a Domain Controller?

### Q3. What is the difference between AD DS and a Domain Controller?

### Q4. Why does Active Directory need DNS?

### Q5. What is a domain?

### Q6. What is a forest?

### Q7. What is a Global Catalog?

### Q8. What is DSRM?

### Q9. Why do we configure a static IP on a Domain Controller?

### Q10. Why shouldn't we point the DC's DNS to `8.8.8.8` as its primary DNS?

Don't worry if you can't answer all 10 yet. **That's exactly what we're going to learn.**

---

## 🚨 Your practical task now

**Stop after Step 2 first.**

Configure:

```text
IP:       192.168.10.10
Mask:     255.255.255.0
Gateway:  blank
DNS:      127.0.0.1
```

Then run:

```cmd
ipconfig
ping 127.0.0.1
ping 192.168.10.10
```

**Send me a screenshot of the `ipconfig` result.**

I'll check your network configuration before you install/promote AD. This is how we'll work throughout your 3-month journey: **one practical checkpoint at a time, with troubleshooting rather than blindly moving to the next step.**
