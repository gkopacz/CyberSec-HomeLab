# 🗄️ Day 03 — Active Directory Deployment & Domain Join

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Windows%20Server-2019-lightgrey?logo=windows)
![Client1](https://img.shields.io/badge/Windows%2010-blue?logo=windows)
![Client2](https://img.shields.io/badge/Windows%2011-blueviolet?logo=windows)
![Status](https://img.shields.io/badge/status-in--progress-yellow)

## 🎯 Objective

Deploy a **Windows Server 2019** VM in the `AD` subnet and promote it to a **Domain Controller**. Set up **Active Directory**, configure **DNS**, and enable **DHCP** for the subnet. Join Windows clients to the new domain to validate identity management and directory-based authentication. This lays the foundation for centralized access control, Group Policy testing, and future detection engineering scenarios.


## 🧠 Skills Demonstrated

- Deployment of **Windows Server 2019** on Hyper-V with Gen 2 settings  
- Promotion of standalone server to **Active Directory Domain Services (AD DS)**  
- Configuration of **DNS Server** for internal name resolution  
- (Optional) Setup of **DHCP Server** for automated IP assignment in the AD zone  
- Creation of an **Active Directory forest and domain** (e.g., `lab.local`)  
- Domain join of Windows 10 and 11 clients and login validation  
- Basic troubleshooting of domain-related connectivity and authentication issues

## 🛠️ Setup Walkthrough

The Windows Server 2019 machine will serve as the backbone of the Active Directory (AD) zone in my lab. Deployed as a virtual machine on Hyper-V, it will be promoted to a Domain Controller, provide DNS and DHCP (for the AD subnet), and eventually manage user/group policies and domain authentication for Windows clients.

I started by downloading the Windows Server 2019 ISO from the official Microsoft Evaluation Center: 🔗 [Download Windows Server 2019](https://www.microsoft.com/en-in/evalcenter/evaluate-windows-server-2019)

### 1️⃣ Create the Windows Server VM

In **Hyper-V Manager**, I created a new Gen 2 virtual machine:

| Setting       | Value                           |
|---------------|---------------------------------|
| **Name**      | DC01-WinServer2019              |
| **Generation**| Gen 2 (UEFI, Secure Boot off)   |
| **CPU**       | 2 vCPU                          |
| **Memory**    | 4 GB (Dynamic)                  |
| **Disk**      | 60 GB (Dynamically Expanding)   |
| **NIC**       | Internal switch (AD subnet)     |

### 2️⃣ Installing Windows Server 2019

I mounted the **Windows Server 2019 ISO** in the virtual DVD drive of the Hyper‑V VM and powered it on.  

To ensure a smooth install, I reviewed and adjusted the **boot order** in the VM settings:

1. **DVD Drive** (for ISO boot)
2. **Hard Drive** (for post-install boots)
3. **Network Adapter** (PXE boot — left last)

> 💡 Setting the correct boot sequence avoids boot loops and ensures the OS installer launches on first boot.

Once installation is complete, I’ll remove the **DVD drive** from the VM to prevent accidental reboots into the installer.

#### 🗣️ Language & Keyboard

Selected the default options:

  * Language: English (United States)
  * Time and currency: English (United States)
  * Keyboard: US

#### 📦 Choose Edition

During setup, I was prompted to select the Windows Server edition. I went with:

> **Windows Server 2019 Datacenter Evaluation (Desktop Experience)**

![Choose Edition](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-OS-version.png)

Why Datacenter?

- It's better optimized for **Hyper-V environments**.
- It includes advanced features like **Shielded VMs**, **Storage Spaces Direct**, and **Software-Defined Networking**, which may come in handy in future lab phases.

> 📚 Microsoft provides a full breakdown of **Standard vs Datacenter** features [here](https://learn.microsoft.com/en-us/windows-server/get-started/editions-comparison?pivots=windows-server-2019&tabs=full-comparison).

For a homelab, either edition works — but Datacenter ensures maximum compatibility with virtualization tasks and gives you flexibility down the line.

#### 🔧 Install Type

I selected **Custom: Install Windows only (Advanced)** to perform a clean, manual installation.

This option allowed me to partition the virtual disk from scratch and ensured no pre-installed roles or features were added — ideal for a domain controller build where I want full control over each configuration step.

#### 💽 Disk Partition

During setup, I clicked **New** and allocated the entire **60 GB** of virtual disk space to a single **primary partition**, then hit **Apply**.

![Disk_Partition_size](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-Disk-Partition.png)

Once the partition table was created, I selected the primary partition and continued with installation.

![Disk_Partition](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-Disk-Partition-done.png)

#### 🔑 Administrator Password Setup

After installation completed, I was prompted to set a password for the **built-in Administrator** account.

I chose a strong, complex password that meets Active Directory best practices.

### 3️⃣ Initial Network Configuration

After logging in to the newly installed **Windows Server 2019** VM, I launched **Server Manager** to perform initial configuration steps.

#### 🖥️ Rename the Host

By default, Windows assigns a random hostname. I renamed it to something meaningful:

- **Old Name:** WIN-XXXXXXXXXX  
- **New Name:** `DC01`

To rename the server, I opened Server Manager, clicked on Local Server, and selected the Computer Name field. From there, I hit Change, entered `DC01` as the new name, and rebooted when prompted.

![Hostname](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-hostname.png)

#### 🌐 Configure Static IP Address

Since I planned to handle DHCP at the Windows Server level, I didn’t configure DHCP on pfSense for the AD subnet. Instead, I manually assigned a static IP address to the domain controller during initial setup to ensure stability for DNS and future domain operations.

Steps:
1. Go to **Control Panel** → **Network and Sharing Center**
2. Click **Change adapter settings**
3. Right-click **Ethernet adapter** → **Properties**
4. Select **Internet Protocol Version 4 (TCP/IPv4)** → **Properties**
5. Configure:
   - IP Address: `10.0.3.9`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: `10.0.3.1` 
   - Preferred DNS: `10.0.3.9` (this server will run DNS after domain promotion)

> 💡 Assigning a static IP ensures domain services remain accessible and clients can reliably resolve and contact the domain controller.

![IP_Address](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-static-ip.png)

### 4️⃣ Install AD DS, DNS & DHCP Roles

I launched **Server Manager** and selected **Add Roles and Features**, then enabled the following:

- **Active Directory Domain Services (AD DS)**
- **DNS Server**
- **DHCP Server**

This prepares the server to become a fully functional domain controller.

#### 🏰 Promote the Server to Domain Controller

After installation, a yellow warning icon appeared in Server Manager — I clicked **"Promote this server to a domain controller"**.

I selected:

- **Add a new forest**
- Root domain name: `lab.local`
- Set a strong **DSRM password** (used for Directory Services recovery)
- Kept default paths for database, logs, and SYSVOL

> 💡 After completing the wizard, the system automatically rebooted. Once it came back online, the server was now an official **Domain Controller**.

### 5️⃣ Configure Static IP & DNS on the Domain Controller


### 6️⃣ Configure DHCP on DC

- Create a new IPv4 scope in **DHCP Manager**
- Define IP range (e.g., `10.0.2.100 - 10.0.2.200`)
- Set DNS server to `10.0.2.1` (DC IP)
- Activate scope

> 💡 On pfSense: make sure DHCP is disabled for the AD interface to avoid conflict

### 7️⃣ Windows Client Setup & Domain Join

I spun up a **Windows 10 Pro** VM in Hyper-V:

- Assigned NIC to the AD internal switch
- Installed updates & renamed machine
- Opened `System Properties` → `Domain Join`
- Joined `lab.local` using AD credentials
- Rebooted and logged in as domain user

### ✅ Validation

- `ipconfig /all` shows DNS = DC IP
- `ping dc.lab.local` resolves successfully
- Logged in with domain credentials
- Accessed `Active Directory Users & Computers`
- Created test user & OU structure

## 🔜 Next Step

On Day 04, I’ll configure Splunk and begin sending Windows logs from the DC and clients. This will kick off the **detection engineering** and centralized monitoring phase of the HomeLab.

