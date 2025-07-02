# 🐉 Day 02 — Kali Linux VM Setup & pfSense Firewall Rules

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Kali%20Linux-2025.2-purple?logo=kali-linux)
![Firewall](https://img.shields.io/badge/firewall-pfSense-red?logo=pfsense)
![OS](https://img.shields.io/badge/OS-Kali%20Linux-informational?logo=kalilinux)
![Status](https://img.shields.io/badge/status-in--progress-yellow)
![Kali Linux](https://img.shields.io/badge/Kali-Linux-blue?logo=kali-linux)


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
