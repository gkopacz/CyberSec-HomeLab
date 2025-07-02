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

## ⚙️ VM Configuration in Hyper-V

I created a new **Generation 2** virtual machine using the following specs:

- **Name:** Kali Linux
- **CPU:** 2 vCPU
- **Memory:** 4 GB (Dynamic)
- **Disk:** 50 GB (Dynamically Expanding)
- **Boot:** UEFI (Secure Boot disabled)
- **NIC:** Internal switch (LAN)

![Kali VM Settings](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-Settings.png)

---

## 📥 ISO Boot & Graphical Install

I mounted the **kali-linux-2025.2-installer-amd64.iso** and launched into the **Graphical install** mode:

![Graphical Install](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-Install.png)

---

## 🌐 Locale & Keyboard Setup

- Language: English
- Country: United States
- Locale: `en_US.UTF-8`

![Locale](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Kali-VM-Locale.png)
