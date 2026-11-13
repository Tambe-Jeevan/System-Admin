Welcome to **Day 7** of your SysAdmin journey, Jeevan!

Today, we dive into **Computer Accounts & Secure Channels**. Up until now, we’ve talked a lot about *users* logging in, but how does Active Directory recognize the physical or virtual computers themselves? Let's break it down from absolute zero.

---

# 📚 Day 7: Computer Accounts, Secure Channels, and Troubleshooting

### 1. What is it? (Absolute Beginner Explanation)

Just like humans need an identity card to enter a secure building, **computers also need an account in Active Directory** to prove they belong to the company network.

* **Computer Account:** When you join a Windows PC (like `LAB-CL01`) to `lab.local`, Active Directory automatically creates a computer object inside its database.
* **Secure Channel:** This is a hidden, highly encrypted communication tunnel built between the client computer and the Domain Controller. It uses Kerberos authentication so that both machines trust each other completely.

```text
       [ 💻 Windows Client (LAB-CL01) ]
                     │
                     │ 🔒 (Encrypted Secure Channel)
                     ▼
       [ 🛡️ Domain Controller (LAB-DC01) ]

```

### 2. Why do we need Computer Accounts and Secure Channels?

* **Automatic Password Changes:** To keep the network secure, domain-joined computers automatically change their computer account password in Active Directory every **30 days** behind the scenes. Humans don't have to do anything; the secure channel handles it.
* **Group Policy Delivery:** The Domain Controller needs to know which computer is asking for policies so it can apply the correct security settings.

---

### 🏢 Real-World Company Example

Imagine an employee takes their office laptop home for 6 months and never connects it to the corporate network. During that time, the computer account password expired or drifted out of sync with Active Directory. When the employee brings the laptop back to the office and tries to log in, they get a terrifying red error screen:

> *"The trust relationship between this workstation and the primary domain failed."*
> As a System Administrator, you will fix this exact issue frequently!

---

### 🛠️ Step-by-Step Lab: Managing Computer Accounts via PowerShell

Let's log into your `LAB-DC01` VM and check computer accounts using PowerShell.

1. Open PowerShell as Administrator on `LAB-DC01`.
2. **View all computer accounts in the domain:**
```powershell
Get-ADComputer -Filter * -Properties IPv4Address, OperatingSystem

```


* *Meaning:* Lists all machines registered in your domain directory.


3. **Find a specific computer:**
```powershell
Get-ADComputer -Identity "LAB-DC01" -Properties *

```



---

### 💻 PowerShell Troubleshooting Commands for Secure Channels

When a machine loses trust with the domain, administrators use these powerful commands (run these on the client machine or remotely):

1. **Test the health of the secure channel:**
```powershell
Test-ComputerSecureChannel -Verbose

```


* *Meaning:* Checks if the encrypted tunnel between the computer and the Domain Controller is healthy. It will return `True` if it's working, or `False` if it's broken.


2. **Repair/Reset a broken secure channel:**
```powershell
Test-ComputerSecureChannel -Repair -Credential "LAB\Administrator"

```


* *Meaning:* Forces the computer to reset its relationship and re-establish a healthy encrypted tunnel with the Domain Controller without having to rejoin the domain from scratch.



---

### ⚠️ Common Errors & Troubleshooting

* **Error:** `The trust relationship between this workstation and the primary domain failed.`
* **Cause:** The computer account password in Active Directory does not match the local computer's stored password (usually due to a long disconnection or replication delay).
* **Fix:** Log into the machine using a local administrator account, re-establish network connection to the DC, and run `Test-ComputerSecureChannel -Repair` or simply disjoin and rejoin the domain.



---

### 🎤 Interview Questions You Might Face

1. **What happens when a computer object is deleted from Active Directory while it is turned on?**
* *Answer:* The secure channel breaks immediately. The next time the user tries to log in with a domain account, authentication will fail because the computer can no longer validate its identity against the directory database.


2. **How often do Windows domain-joined computers automatically update their computer account passwords?**
* *Answer:* By default, every 30 days.



---

### 🎯 Your Practical Task for Today

1. Open PowerShell as Administrator on `LAB-DC01`.
2. Run `Get-ADComputer -Filter *` to verify your domain computer objects.
3. Run `Test-ComputerSecureChannel -Verbose` on your DC to observe how the DC checks its own secure channel.