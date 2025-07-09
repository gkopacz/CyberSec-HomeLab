# 🗄️ Day 03 — Active Directory Deployment & Domain Join

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Windows%20Server-2019-lightgrey?logo=windows)
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

I started by downloading the Windows Server 2019 ISO from the official Microsoft Evaluation Center: 🔗 Download Windows Server 2019

### 1️⃣ Create the Windows Server VM

In **Hyper-V Manager**, I created a new Gen 2 virtual machine:

| Setting       | Value                          |
|---------------|---------------------------------|
| **Name**      | DC-WinServer                   |
| **Generation**| Gen 2 (UEFI, Secure Boot off)   |
| **CPU**       | 2 vCPU                          |
| **Memory**    | 4 GB (Dynamic)                  |
| **Disk**      | 60 GB (Dynamically Expanding)   |
| **NIC**       | Internal switch (AD subnet)     |

I mounted the Windows Server 2019 ISO and booted into setup. After completing the OS installation, I renamed the server and applied updates.

> 💡 Make sure the VM gets an IP from the AD subnet (via pfSense or DHCP on DC later).

### 2️⃣ Promote to Domain Controller

#### a. Add AD DS Role:
- Launch **Server Manager** → `Add Roles and Features`
- Enable: 
  - Active Directory Domain Services
  - DNS Server
  - (Optional) DHCP Server

#### b. Promote the Server:
- Click `Promote this server to a domain controller`
- Create a new forest: `lab.local`
- Set DSRM password (Directory Services Restore Mode)
- Accept default paths and install

> 💡 The system will reboot after promotion. Once done, your server is now a DC!

### 3️⃣ Optional: Configure DHCP on DC

If you want the DC to manage DHCP for the `AD` subnet:

- Create a new IPv4 scope in **DHCP Manager**
- Define IP range (e.g., `10.0.2.100 - 10.0.2.200`)
- Set DNS server to `10.0.2.1` (DC IP)
- Activate scope
- On pfSense: Disable DHCP for the AD interface to avoid conflict

### 4️⃣ Windows Client Setup & Domain Join

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

