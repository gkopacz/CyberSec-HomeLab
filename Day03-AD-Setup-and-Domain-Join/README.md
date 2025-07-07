# 🗄️ Day 03 — Active Directory Deployment & Domain Join

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Windows%20Server-2019-lightgrey?logo=windows)
![Status](https://img.shields.io/badge/status-in--progress-yellow)

## 🎯 Objective

Deploy a **Windows Server 2019** VM on the `AD` subnet and promote it to a **Domain Controller**. Configure DNS and optionally DHCP services. Join a Windows 10/11 client to the domain. This establishes centralized identity management and paves the way for future GPO testing, authentication logging, and privilege escalation simulation.

## 🧠 Skills Demonstrated

- Windows Server 2019 installation and Hyper-V VM config
- Promoting a standalone server to an AD DS domain controller
- Installing and configuring DNS and optionally DHCP
- Creating a new AD forest and domain (e.g., `lab.local`)
- Joining a Windows client to Active Directory
- Validating domain services (DNS resolution, authentication, tools)

## 🛠️ Setup Walkthrough

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

