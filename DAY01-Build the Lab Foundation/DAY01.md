Understood, Jeevan. Let's keep our training format clean, structured, and strictly synchronized with your exact progression.

With your upgraded **32 GB RAM** on host **`RAMAYANA`**, we are executing **Day 1** of your roadmap with a proper multi-VM lab design in mind.

---

### 🟢 TODAY: Day 1 — Build the Lab Foundation

#### Step 1 — Verify Hyper-V

On your laptop (`RAMAYANA`), ensure your hypervisor features are active:

* Start → Search → **Turn Windows features on or off**

* Check:
* ☑ **Hyper-V**
* ☑ **Hyper-V Management Tools**
* ☑ **Hyper-V Platform**






#### Step 2 — Open Hyper-V Manager

Press `Win + R`, type `virtmgmt.msc`, and press Enter. Ensure you see your host:

```text
Hyper-V Manager
│
└── RAMAYANA

```

#### Step 3 — Create our laboratory network

* In Hyper-V Manager, open **Virtual Switch Manager**.


* Select **Internal** under network types and click **Create Virtual Switch**.


* Name it: `LabNetwork`.



#### Step 4 — Create our first VM (`LAB-DC01`)

* Hyper-V Manager → **New** → **Virtual Machine**.


* **VM name:** `LAB-DC01`

* **Generation:** Choose **Generation 2**.


* **Memory:** Set startup memory to `4096 MB` (4 GB) and leave Dynamic Memory **disabled**. *(Because you upgraded to 32 GB RAM, we can give our Primary Domain Controller more breathing room than your previous 16 GB setup).*
* **Network:** Select `LabNetwork`.


* **Virtual hard disk:** Create `LAB-DC01.vhdx` with a size of `60 GB`.


* **Installation Media:** Select your Windows Server Evaluation ISO file and click **Finish**.



#### Step 5 — Adjust Settings Before Booting

Right-click `LAB-DC01` → **Settings**:

* **Processor:** Set to `2 virtual processors` (conserving your i5-6300U logical processor limit).

#### Step 6 — Start Windows Server Installation

* Right-click `LAB-DC01` → **Connect** → Click the green **Start** button.


* Quickly click inside the virtual screen and press any key to boot from the ISO.


* When the setup wizard appears, choose **Windows Server Standard Evaluation (Desktop Experience)**.

---

### 🎯 Your Task Right Now

Stop here, complete these exact steps, and **send me a confirmation or screenshot once you have reached the Windows Server installation screen**.

Once you are there, we will proceed step-by-step through static IP assignment and Active Directory domain promotion!