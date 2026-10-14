Absolutely. Your upgraded laptop is now **much better for a System Administrator practice lab**.

You now have **32 GB RAM**, which gives us enough room to build a small enterprise-style virtual environment with multiple VMs instead of practicing only on one Domain Controller. Your original roadmap also expects you to progress from AD fundamentals into sites/replication, backup, hybrid identity, security, PowerShell and troubleshooting. 

## 🖥️ Your new SysAdmin Lab

We will **not** immediately create 5–6 VMs. We'll build them progressively so you understand what each server does.

### Target lab

```text
                    YOUR PHYSICAL LAPTOP
                    RAMAYANA
              Windows 11 Pro | 32 GB RAM
                         │
                    Hyper-V Manager
                         │
              ┌──────────┴──────────┐
              │                     │
          LabNetwork             Internet
        (Internal Switch)       (Optional)
              │
      ┌───────┼────────┬──────────┐
      │       │        │          │
   LAB-DC01  LAB-DC02  LAB-SRV01  LAB-CL01
      │       │        │          │
      └───────┴────────┴──────────┘
                Active Directory
                   lab.local
```

Eventually we'll add:

```text
LAB-DC01   → Primary Domain Controller + DNS
LAB-DC02   → Additional Domain Controller + DNS
LAB-SRV01  → Member Server / File Server
LAB-CL01   → Windows 11 Domain Client
LAB-LNX01  → Linux Server
```

This will give you practical experience with **DCs, member servers, clients, DNS, DHCP, GPO, file permissions, PowerShell, Linux, troubleshooting and eventually replication/backup**.

---

# 🔥 First: your 3-month training structure

We will continue the plan you already established rather than starting a completely new course.

Your existing plan is:

| Month       | Main objective                                     |
| ----------- | -------------------------------------------------- |
| **Month 1** | Active Directory + Identity + GPO                  |
| **Month 2** | Infrastructure + Replication + Backup + Hybrid     |
| **Month 3** | Security + Auditing + PowerShell + Job preparation |

Your source roadmap specifically has **60 days of daily practice**, with approximately **45 minutes theory + 75 minutes hands-on lab**. 

So our daily format will be:

### 📚 Daily 2-hour format

**45 minutes — Learn**

* Concept
* Architecture
* Commands
* Real-world example
* Interview questions

**75 minutes — Practice**

* Build something
* Configure it
* Break it intentionally
* Troubleshoot it
* Verify it
* Document it

---

# 🟢 TODAY: Day 1 — Build the Lab Foundation

Because you've changed your SSD and upgraded to **32 GB RAM**, don't start with AD immediately.

We first establish a clean virtualization environment.

## Step 1 — Verify Hyper-V

On your laptop:

**Start → Search → Turn Windows features on or off**

You should have:

```text
☑ Hyper-V
   ☑ Hyper-V Management Tools
   ☑ Hyper-V Platform
```

Your hardware already reports:

```text
Hyper-V - VM Monitor Mode Extensions : Yes
Hyper-V - SLAT                       : Yes
Virtualization Enabled in Firmware   : Yes
Data Execution Prevention            : Yes
```

So your laptop is suitable for the lab.

---

# Step 2 — Open Hyper-V Manager

Press:

```text
Win + R
```

Type:

```text
virtmgmt.msc
```

Press Enter.

You should see:

```text
Hyper-V Manager
│
└── RAMAYANA
```

---

# Step 3 — Create our laboratory network

This is important.

Open:

**Hyper-V Manager → Virtual Switch Manager**

Select:

```text
Internal
```

Click:

**Create Virtual Switch**

Name it:

```text
LabNetwork
```

Use this network for our isolated lab.

### Why Internal?

Because eventually we want:

```text
DC01 ─────┐
DC02 ─────┤
Server ───┼── LabNetwork
Client ────┤
Linux ─────┘
```

The machines can communicate with each other without putting your experimental AD environment directly onto your home/company network.

Your original setup guide also specifies an **Internal** Hyper-V switch for the lab. 

---

# Step 4 — Create our first VM

Go to:

**Hyper-V Manager → New → Virtual Machine**

### VM name

```text
LAB-DC01
```

Meaning:

```text
LAB = laboratory
DC  = Domain Controller
01  = first DC
```

---

## Step 5 — Generation

Select:

```text
Generation 2
```

---

# Step 6 — Memory

Because you now have 32 GB RAM:

```text
Startup memory: 4096 MB
```

For this first VM:

```text
☐ Use Dynamic Memory
```

Leave it disabled for now.

Your old roadmap used approximately 2–2.5 GB for the DC because the laptop previously had 16 GB RAM. With your upgrade, we can comfortably give the DC more memory.

---

# Step 7 — Network

Select:

```text
LabNetwork
```

Do **not** select your physical Wi-Fi adapter directly.

---

# Step 8 — Virtual hard disk

Create:

```text
LAB-DC01.vhdx
```

Size:

```text
60 GB
```

This is virtual storage; it doesn't mean Windows will immediately consume 60 GB of physical SSD space if we're using a dynamically expanding VHDX.

---

# Step 9 — Windows Server ISO

You already have the Windows Server Evaluation ISO that was used in your previous setup. Your uploaded lab document records the Windows Server Evaluation ISO as part of the original lab setup. 

Select:

```text
Install an operating system from a bootable image file
```

Browse to your `.iso`.

Then:

**Finish**

---

# Step 10 — Before starting the VM

This time I want you to learn something important as a future SysAdmin.

Don't immediately press Start.

Right-click:

```text
LAB-DC01
```

→ **Settings**

Check:

```text
Memory
Processor
Network Adapter
DVD Drive
Hard Drive
```

For CPU, start with:

```text
2 virtual processors
```

Your physical CPU is only **2 cores / 4 logical processors**, so we should be conservative with CPU allocation.

---

# Step 11 — Start Windows Server installation

Right-click:

```text
LAB-DC01
```

→ **Connect**

Then:

```text
Start
```

If you see:

```text
Press any key to boot from CD or DVD...
```

click inside the VM window and press a key.

---

# Step 12 — Windows Server edition

When Windows Setup asks for edition, choose:

```text
Windows Server Standard Evaluation
(Desktop Experience)
```

### Important

Choose:

```text
Desktop Experience
```

not:

```text
Server Core
```

for our first lab.

We will later deliberately create a **Server Core VM** because a SysAdmin should be comfortable with both GUI and command-line administration.

---

# 🧪 Our VM progression

This is where your new lab becomes much more interesting.

We will build the following **one by one**:

### VM 1 — LAB-DC01

```text
Windows Server
        ↓
AD DS
        ↓
DNS
        ↓
Domain Controller
        ↓
lab.local
```

This is our first milestone.

Your previous guide's AD promotion sequence is:

**Add new forest → lab.local → DNS → Global Catalog → DSRM password → prerequisite check → Install → reboot.** 

---

### VM 2 — LAB-CL01

```text
Windows 11
     ↓
Join lab.local
     ↓
Domain User Login
     ↓
GPO Testing
```

Here you will learn what actually happens when a normal employee computer joins a domain.

---

### VM 3 — LAB-SRV01

```text
Windows Server
      ↓
Member Server
      ↓
File Server
      ↓
NTFS Permissions
      ↓
Share Permissions
      ↓
Department Shares
```

This is extremely important for SysAdmin interviews.

---

### VM 4 — LAB-DC02

Later:

```text
LAB-DC01
    │
    │ AD Replication
    ▼
LAB-DC02
```

Then we'll learn:

* Additional Domain Controller
* DNS
* Global Catalog
* AD replication
* `repadmin`
* `dcdiag`
* FSMO
* failure scenarios

Your roadmap specifically places **AD Sites, Subnets, replication, `repadmin /showrepl`, `dcdiag`, DNS SRV records, and DC promotion/demotion** in Month 2. 

---

### VM 5 — LAB-LNX01

Later:

```text
Linux Server
     ↓
SSH
     ↓
Users
     ↓
Permissions
     ↓
Services
     ↓
Networking
```

This will help bridge your Linux/DevOps knowledge with SysAdmin administration.

---

# 🧠 What we're ultimately building

By the end of the 3 months, your home lab should look something like:

```text
                         RAMAYANA
                    Windows 11 Pro
                         │
                       Hyper-V
                         │
                 ┌──── LabNetwork ────┐
                 │                    │
                 ▼                    ▼
             LAB-DC01              LAB-DC02
             4 GB RAM              4 GB RAM
             AD DS                 AD DS
             DNS                   DNS
                 │                    │
                 └────────┬───────────┘
                          │
                    lab.local
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
         LAB-CL01     LAB-SRV01     LAB-LNX01
        Win 11 Client  File Server    Linux
```

We won't build all of this today. **You will build it yourself over the 60 days.** That's the important part.

---

# 📅 Your next 10 days

To make our daily practice clear:

| Day               | Practical Lab                        |
| ----------------- | ------------------------------------ |
| **Day 1**         | Hyper-V + LAB-DC01                   |
| **Day 2**         | AD DS + create `lab.local`           |
| **Day 3**         | ADUC + Users + Groups + OUs          |
| **Day 4**         | Domain join Windows 11 client        |
| **Day 5**         | DNS fundamentals + troubleshooting   |
| **Day 6**         | AD users/groups + PowerShell         |
| **Day 7**         | Computer accounts + secure channel   |
| **Day 8**         | Group Policy fundamentals            |
| **Day 9**         | Create practical GPOs                |
| **Day 10**        | GPO troubleshooting                  |
| **Day 11 onward** | Continue the existing 60-day roadmap |

This follows the same progression in your original roadmap: AD architecture first, then object management, PowerShell and GPOs. 

---

# ⚠️ One important rule for your lab

**Do not use your company's real domain, users, passwords, IP ranges, or company machines in this lab.**

Everything we create will be:

```text
LAB
```

and isolated.

For example:

```text
Domain:       lab.local
DC:           192.168.10.10
DC02:         192.168.10.11
Server:       192.168.10.20
Client:       192.168.10.30
```

These are **lab-only addresses**.

---

# 🎯 Your task right now

Don't go further than this.

### Complete only:

**1.** Verify Hyper-V
**2.** Open Hyper-V Manager
**3.** Create `LabNetwork`
**4.** Create `LAB-DC01`
**5.** Configure:

```text
Generation:       2
RAM:              4096 MB
CPU:              2
VHDX:             60 GB
Network:          LabNetwork
ISO:              Windows Server Evaluation
```

**6.** Start Windows Server installation.

When you reach the Windows Server installation screen, send me a **screenshot**.

I'll take you through the **next screen one at a time**, including exactly what to select and why. This way you're actually learning SysAdmin administration rather than simply following a giant checklist.
