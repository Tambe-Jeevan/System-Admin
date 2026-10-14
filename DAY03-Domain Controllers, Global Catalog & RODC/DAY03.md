Welcome to **Day 3** of your SysAdmin journey, Jeevan!

Now that your infrastructure "brain" (`LAB-DC01`) is healthy and your DNS is clean, we are stepping inside Active Directory to organize how real companies manage their employees and computers.

---

# 📚 Day 3: Active Directory Users, Groups, and OUs (Organizational Units)

### 1. What is it? (Absolute Beginner Explanation)

Imagine walking into a massive multi-floor corporate building with 5,000 employees. If every employee's file, computer, and keycard were thrown into one giant filing cabinet, finding anything would be impossible.

* **Active Directory Users and Computers (ADUC):** This is the master database tool (`dsa.msc`) where we manage everything in our domain.
* **Organizational Units (OUs):** Think of OUs as **Departments or Filing Drawers** inside your company (e.g., IT Department, Sales Department, HR Department).
* **Users:** Digital accounts that allow humans to log into computers.
* **Groups:** Collections of users. Instead of giving 50 separate IT employees folder permissions one by one, we put them into an **IT-Admins group** and give permission to the group *once*.

Here is a visual of how a company organizes its digital office:

```text
📁 lab.local (The Root Company Domain)
│
└── 📂 Corp (Organizational Unit)
     ├── 📂 IT (Sub-Department OU)
     │    ├── 👤 User: John Doe (IT Support)
     │    └── 👥 Group: IT-Admins (Global Security Group)
     │
     └── 📂 Sales (Sub-Department OU)
          ├── 👤 User: Jane Smith (Sales Rep)
          └── 👥 Group: Sales-Team (Global Security Group)

```

### 2. Why do we need OUs instead of default folders?

By default, AD comes with built-in folders like `Users` and `Computers`. However, these are **Containers**, not OUs.

* **Containers** cannot have Group Policy Objects (GPOs) linked to them.
* **OUs** allow you to apply specific rules (like desktop wallpapers, security restrictions, or software installs) to *only* that specific department.

---

### 🛠️ Step-by-Step Lab: Building Your Corporate Structure

Let's log into `LAB-DC01` and build our practice company structure.

#### Step 1 — Open the ADUC Console

1. Inside your `LAB-DC01` VM, press **Win + R**, type `dsa.msc`, and press **Enter**.
2. You will see your domain root (`lab.local`) on the left panel.

#### Step 2 — Create the Top-Level OU (`Corp`)

1. Right-click **`lab.local`** → Select **New** → **Organizational Unit**.
2. Name it: `Corp`
3. *(Notice the checkbox: "Protect container from accidental deletion". Leave it checked—this is a best practice in real companies so nobody accidentally deletes a whole department!)*
4. Click **OK**.

#### Step 3 — Create Departmental Sub-OUs (`IT` and `Sales`)

1. Right-click your newly created **`Corp`** OU → Select **New** → **Organizational Unit**.
2. Name it: `IT` → Click **OK**.
3. Repeat the process: Right-click **`Corp`** → **New** → **Organizational Unit** → Name it `Sales` → Click **OK**.

#### Step 4 — Create a Test User and a Security Group

1. Expand `Corp` and click on your **`IT`** OU.
2. In the empty space on the right, right-click → **New** → **User**.
* **First name:** John
* **Last name:** Doe
* **User logon name:** `jdoe`
* Click **Next**.
* Set a password (e.g., `P@ssw0rd123!`).
* Uncheck *"User must change password at next logon"* (for lab convenience) and check *"Password never expires"*.
* Click **Next** → **Finish**.


3. Now, right-click inside the **`IT`** OU → **New** → **Group**.
* **Group name:** `IT-Admins`
* **Group scope:** Global
* **Group type:** Security
* Click **OK**.


4. Double-click your new **`IT-Admins`** group → Go to the **Members** tab → Click **Add** → Type `jdoe` → Click **Check Names** → Click **OK** → **Apply**.

---

### 💻 Commands & Verification

As a SysAdmin, you can also manage AD objects using PowerShell. Open PowerShell as Administrator on `LAB-DC01` and run these verification commands:

* **View all OUs in the domain:**
```powershell
Get-ADOrganizationalUnit -Filter *

```


* **Verify your new user exists inside the IT OU:**
```powershell
Get-ADUser -Filter "Name -like 'John Doe'" -Properties SamAccountName, DistinguishedName

```



#### How to Verify:

In your ADUC window (`dsa.msc`), expand `lab.local` $\rightarrow$ `Corp` $\rightarrow$ `IT`. You should visually see `John Doe` and `IT-Admins` sitting neatly inside the IT department drawer.

---

### ⚠️ Common Errors & Troubleshooting

* **Error:** *"Windows cannot create the object... because: Access is denied."*
* **Cause:** You are logged in with a local account instead of the Domain Administrator account.
* **Fix:** Ensure you are logged into `LAB-DC01` using `LAB\Administrator`.


* **Error:** When trying to delete an OU, it says you don't have permissions or it's protected.
* **Fix:** Remember we checked "Protect container from accidental deletion". To delete a protected OU, right-click the OU $\rightarrow$ Properties $\rightarrow$ Object tab $\rightarrow$ Uncheck "Protect object from accidental deletion".



---

### 🎤 Interview Questions You Might Face

1. **What is the difference between a Container and an Organizational Unit (OU)?**
* *Answer:* Containers are default system folders (like Users/Computers) where you cannot link Group Policies. OUs are custom-created containers that allow you to organize objects hierarchically and apply Group Policy Objects (GPOs) and delegation of control.


2. **Why do we use Security Groups instead of assigning permissions directly to individual users?**
* *Answer:* Management scalability. If 20 people join the IT department, managing permissions for 20 individual accounts is a nightmare. Instead, you add them to the `IT-Admins` group, and assign permissions to the group once.



---

### 🎯 Your Practical Task for Today

1. Complete the lab steps above inside your `LAB-DC01` ADUC console (Create the `Corp` OU, `IT` and `Sales` sub-OUs, create user `John Doe`, and group `IT-Admins`).
2. Take a screenshot or verify via PowerShell.
3. Once done, let me know, and we will move straight into **Day 4: Setting up `LAB-CL01` (Windows Client) and joining it to `lab.local**`!