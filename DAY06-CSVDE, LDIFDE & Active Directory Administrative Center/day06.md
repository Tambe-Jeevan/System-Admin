Welcome to **Day 6** of your SysAdmin journey, Jeevan!

Today, we transition from clicking through graphical user interfaces (GUI) to managing Active Directory like a true systems engineer using **PowerShell**.

---

# 📚 Day 6: AD Users, Groups, and Bulk Management via PowerShell

### 1. What is it? (Absolute Beginner Explanation)

Imagine you get a ticket on Monday morning: *"Please create 50 new employee accounts for the Sales department."*

* If you use the graphical **ADUC** (`dsa.msc`) tool we used on Day 3, you would have to right-click $\rightarrow$ New User $\rightarrow$ type the name $\rightarrow$ type the password $\rightarrow$ set options $\rightarrow$ click finish, **50 times**. That would take hours.
* **PowerShell:** This is Microsoft's powerful command-line shell and scripting language. With a simple PowerShell script, an administrator can create all 50 accounts, add them to security groups, and assign folders in **less than 2 seconds**.

```text
       [ ⌨️ PowerShell Script ]
                  │
                  │ (Processes 50 accounts instantly)
                  ▼
       [ 🛡️ Active Directory Database ]

```

### 2. Why do we need PowerShell for Active Directory?

* **Speed & Automation:** Enterprise IT is about eliminating repetitive manual work.
* **Consistency:** Doing things manually leads to human typos. Scripts ensure every user account follows the exact same naming convention and security template.
* **Remote Management:** SysAdmins often manage servers sitting hundreds of miles away where graphical tools are too slow or unavailable.

---

### 🔍 Key PowerShell Active Directory Cmdlets

PowerShell commands (called *cmdlets*) follow a simple **Verb-Noun** pattern:

* `Get-ADUser` $\rightarrow$ Finds or retrieves user accounts.
* `New-ADUser` $\rightarrow$ Creates a brand-new user.
* `Add-ADGroupMember` $\rightarrow$ Adds a user into a security group.
* `Disable-ADAccount` $\rightarrow$ Instantly deactivates a user account (e.g., when someone leaves the company).

---

### 🏢 Real-World Company Example

When a company merges with another or hires a large batch of interns, IT departments do not click through menus. They export the list of names from an Excel spreadsheet into a `.csv` file, write a 5-line PowerShell loop, and provision hundreds of identities instantly.

---

### 🛠️ Step-by-Step Lab: Managing AD via PowerShell

Let's log into your `LAB-DC01` VM and run some real administrative commands.

1. Inside `LAB-DC01`, click Start, type `powershell`, right-click **Windows PowerShell**, and select **Run as administrator**.
2. **Find an existing user:**
Let's search for the `John Doe` user we created on Day 3:
```powershell
Get-ADUser -Filter "Name -like '*John*'" -Properties EmailAddress, Department

```


3. **Create a new user using PowerShell:**
Let's create a new user named *Sarah Connor* directly from the command line inside our `IT` OU:
```powershell
New-ADUser -Name "Sarah Connor" -GivenName "Sarah" -Surname "Connor" -SamAccountName "sconnor" -UserPrincipalName "sconnor@lab.local" -Path "OU=IT,OU=Corp,DC=lab,DC=local" -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) -Enabled $True

```


4. **Add the new user to a security group:**
Let's add Sarah to our `IT-Admins` group:
```powershell
Add-ADGroupMember -Identity "IT-Admins" -Members "sconnor"

```



---

### 💻 Verification & How to Check Your Work

Run this command to verify that Sarah was successfully created and added to the group:

```powershell
Get-ADGroupMember -Identity "IT-Admins"

```

*Expected Output:* You should see both `John Doe` (`jdoe`) and `Sarah Connor` (`sconnor`) listed inside the `IT-Admins` security group! You can also open ADUC (`dsa.msc`) to see her appear visually inside the IT department.

---

### ⚠️ Common Errors & Troubleshooting

* **Error:** `The term 'New-ADUser' is not recognized as the name of a cmdlet...`
* **Cause:** The Active Directory PowerShell module is missing or not loaded. (This usually happens on client PCs, but on a Domain Controller, it should be native).
* **Fix:** Run this command to install the module on the server: `Add-WindowsFeature RSAT-AD-PowerShell`



---

### 🎤 Interview Questions You Might Face

1. **Why is PowerShell preferred over GUI tools for bulk user management?**
* *Answer:* PowerShell allows for automation, repeatability, and massive time savings. Creating hundreds of accounts manually through ADUC is prone to human error and inefficiency, whereas a script can execute it flawlessly in seconds.


2. **What cmdlet would you use to find all disabled user accounts in your domain?**
* *Answer:* `Search-ADAccount -AccountDisabled` or `Get-ADUser -Filter "Enabled -eq $false"`.



---

### 🎯 Your Practical Task for Today

1. Open PowerShell as Administrator on `LAB-DC01`.
2. Run the `New-ADUser` command provided above to create a second test user (e.g., *Alex Smith* / `asmith`).
3. Use `Get-ADUser -Filter *` to list all users in your lab domain.