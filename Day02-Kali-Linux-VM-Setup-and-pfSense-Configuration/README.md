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

After setting the hostname, I skipped the domain name section since I’m not joining this VM to any domain - it’ll operate standalone within LAN subnet.

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






