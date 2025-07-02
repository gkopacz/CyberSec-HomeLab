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

## 🏗️ Setup Walkthrough

I started by heading over to the [official Kali Linux download page](https://www.kali.org/get-kali/#kali-platforms) and selecting the **Installer Images** for the **x64 (Installer)** platform.

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

> 💡 I chose **Generation 2** for UEFI support and modern hardware emulation. Make sure to disable **Secure Boot**, as Kali doesn’t support the default Microsoft boot keys.

Once the VM was created, I attached the **Kali Linux 2025.2 ISO** to the virtual DVD drive and adjusted the boot order so the system would boot directly into the installer.

![Kali VM Settings](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-Settings.png)






## 📥 ISO Boot & Graphical Install

I mounted the **kali-linux-2025.2-installer-amd64.iso** and launched into the **Graphical install** mode:

![Graphical Install](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-Install.png)

---

## 🌐 Locale & Keyboard Setup

- Language: English
- Country: United States
- Locale: `en_US.UTF-8`

![Locale](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-Locale.png)
