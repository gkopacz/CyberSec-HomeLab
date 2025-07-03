# 🐉 Day 02 — Kali Linux VM Setup & pfSense Firewall Rules

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Kali%20Linux-2025.2-purple?logo=kali-linux)
![Firewall](https://img.shields.io/badge/firewall-pfSense-red?logo=pfsense)
![Status](https://img.shields.io/badge/status-in--progress-yellow)

## 🎯 Objective

Install **Kali Linux 2025.2** manually on Hyper-V, connect it to the lab’s LAN subnet, and access the **pfSense web GUI** to configure the initial firewall rules.  
This step brings the *attacker zone* online and initiates security policy enforcement within the lab environment.

## 🧠 Skills Demonstrated

- Manual installation of **Kali Linux 2025.2** using ISO on Hyper-V  
- Hyper-V virtual hardware configuration (Gen2, UEFI, NIC assignment)  
- Integration of Kali VM with isolated lab subnet via internal switch  
- DHCP IP assignment validation via pfSense  
- Access and authentication to pfSense web GUI  
- Creation of initial firewall rules for segmentation and access control  

# 🛠️ Setup Walkthrough

I started by heading over to the [official Kali Linux download page](https://www.kali.org/get-kali/#kali-platforms) and selecting the **Installer Images** for the **x64** platform.

I chose to perform a **manual installation** using the full ISO. This method works both on bare metal and inside a guest virtual machine. It offers more control over system configuration and is great for showcasing OS installation and hardening skills.

> 💡 If you're looking for a quicker setup, Kali also offers [pre-built VM images](https://www.kali.org/get-kali/#kali-virtual-machines) for platforms like Hyper-V, VMware, and VirtualBox. These come preconfigured and optimized for virtualization — perfect if you want to skip installation steps and get straight to lab work.

Here’s a comprehensive breakdown of the Day 02 setup, with a step‑by‑step walkthrough of the entire Kali VM installation process.

## 1️⃣ Creating the Virtual Machine in Hyper‑V

I created a new virtual machine in **Hyper‑V Manager** using the following configuration:

| Setting    | Value                          |
|------------|---------------------------------|
| **Name**   | Kali Linux                      |
| **Generation** | Gen 2 (UEFI, Secure Boot disabled) |
| **CPU**    | 2 vCPU                          |
| **Memory** | 4 GB (Dynamic)                  |
| **Disk**   | 50 GB (Dynamically Expanding)   |
| **Network**| Internal switch (LAN subnet)    |

Once the VM was created, I attached the **Kali Linux 2025.2 ISO** to the virtual DVD drive and adjusted the boot order so the system would boot directly into the installer.

![Kali VM Settings](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-Settings.png)

> 💡 I chose **Generation 2** for UEFI support, modern hardware emulation, and better compatibility with virtualization features like dynamic memory allocation. Make sure to disable **Secure Boot**, as Kali doesn’t support the default Microsoft boot keys.

## 2️⃣ Booting Kali & Initial Configuration

After attaching the **kali-linux-2025.2-installer-amd64.iso** to the virtual DVD drive, I booted the VM and selected the **Graphical install** option.

![Graphical Install](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-Install.png)

During the initial setup, I configured the following:
- **Language:** English
- **Location:** Other -> Europe -> Romania
- **Locale:** `en_US.UTF-8`
- **Keyboard:** American English (US)

> 💡 The system clock was automatically synced using Kali’s default NTP server. Since I had already selected my region earlier, the installer correctly set the time zone without any manual input.

When prompted for a hostname, I went with the default: `kali`. 

After setting the hostname, I skipped the domain name section since I’m not joining this VM to any domain — it will operate standalone within the LAN subnet.

> 💡 No need to manually configure networking since pfSense’s DHCP server automatically assigned an IP address to the Kali VM via the internal LAN switch. This kept the installation smooth and hands-off at the network stage.

Next, I created a **non-root user** to follow best practices and set a strong password.

I named the account `g0bl1n` 🧟 — because every lab needs a little chaos in a hoodie.

![User](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-username.png)

## 3️⃣ Partitioning the Disk

For the partitioning scheme, I went with **Guided - use entire disk**.

![Partition Method](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-partition-guided.png)

When asked about the partition disk, I chose the default virtual disk provided during the install process.

![Disk Select](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-partition-disk.png)

Then, for simplicity and easier management inside the VM, I went with **All files in one partition**.

![All-in-one Partition](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-partition-disk-full.png)

Here’s the partition summary before writing changes to disk.

![Partition Overview](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-partition-overview.png)

I confirmed the changes and wrote them to disk.

![Confirm Disk Write](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-partition-format.png)

## 4️⃣ Software Selection & Package Install

During the package selection step, I kept things minimal but functional.

I chose:
- **Desktop Environment:** XFCE (lightweight and fast)
- **Top 10 Tools:** Predefined list of essential Kali tools
- **Default tools:** Recommended packages for general use

![Desktop Selection](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-default-desktop.png)

> 💡 I went with XFCE because it runs smoothly inside a VM with modest resources. It’s snappy, stable, and gets the job done without bloat.

After selecting the packages, the system proceeded with the installation and finished with GRUB setup and final config.

![Desktop Selection](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-install-software.png)

## 5️⃣ Post-Install System Prep

Before diving into firewall configuration, I made sure the system was updated and tweaked for virtualization performance.

#### 🔄 System Update

I started by refreshing Kali’s package list and installing updates. This ensures all tools, dependencies, and security patches are fresh out of the oven.

```bash
sudo apt update && sudo apt full-upgrade -y
```

#### ⚙️ Kali Tweaks & Enhanced Session

Kali provides a built-in command-line tool called `kali-tweaks` that simplifies customization for various use cases, especially when running in virtualized environments.

```bash
sudo kali-tweaks
```

I selected the following:
- **Virtualization** → Hyper-V tools and guest enhancements

#### 🖥️ Enable Enhanced Session Mode (Windows Host)

After tweaking inside the VM, I enabled Enhanced Session Mode from the **Windows host** for better usability:

1. I shut down the Kali VM.
2. Opened PowerShell **as Administrator**.
3. Ran this command:

```powershell
Set-VMHost -EnableEnhancedSessionMode $true
Set-VM "Kali Linux" -EnhancedSessionTransportType HVSocket
```

> 💡 This allows features like dynamic display scaling, better mouse handling, and clipboard between host and Kali guest.

#### 🌐 Network Check

Once the system was up and running, I logged into the Kali desktop and ran ifconfig.

The Kali VM received a dynamic IP address from the pfSense DHCP server (10.0.1.101) — exactly as expected for the LAN subnet (10.0.1.0/24).

![Desktop and ping](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-network.png)

> 📌 To align with my original network diagram and keep things tidy, I’ll create a DHCP reservation in pfSense for the Kali VM so it always gets the IP address 10.0.1.47.

## 6️⃣ pfSense Initial Configuration Wizard

Now that Kali is up and running on the LAN subnet, it’s time to start enforcing traffic control using pfSense.

I logged into the pfSense web GUI from the Kali browser at https://192.168.100.30. 

Since pfSense uses a self-signed SSL certificate, Firefox threw a warning — which I bypassed.

![pfSense Init](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/pfSense-init.png)

After accepting the risk, I authenticated using the default admin credentials and launched the setup wizard.

![pfSense Login](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/pfSense-Login.png)

> 💡 The default username is *admin* and password is *pfsense*

#### 🧱 General Settings

I gave my firewall a hostname: `pfSenseFW`  
Domain: `home.arpa`

![pfSense hostname](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/pfSense-hostname.png)

> 💡 I left “Override DNS” **unchecked** so I could control DNS via resolver settings later.

#### ⏰ Time and Region

Set the **time server** to the default and aligned timezone to `Europe/Bucharest` for consistent log timestamps.

![pfSense time](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/pfSense-timezone.png)

#### 🔐 WAN Security Settings

Disabled **RFC1918 blocking** since my WAN IP is on a private range (from home DHCP). Blocking this would’ve cut off my connectivity.

![WAN RFC1918 setting](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/pfSense-rfc1918.png)

#### 🌐 LAN Network

Confirmed that the **LAN interface** was set to `10.0.1.1/24` — matching the diagram and providing the subnet for my lab internal network.

#### 🔑 Securing the Firewall

Changed the **default admin password** to a strong passphrase.

### ✅ Finalizing Setup

Once complete, pfSense prompted to check for updates. I went ahead and upgraded the system to the latest stable version: **pfSense CE 2.8.0**

## 7️⃣ pfSense Firewall Rules 

With the interfaces and static mappings in place, I started building out access control policies between zones.

#### 🔧 Interface Mapping & Naming

First, I renamed the default OPT interfaces for clarity:

- `OPT1` → `MONITORING`
- `OPT2` → `AD`
- `OPT3` → `VULNERABLEMACHINES`










