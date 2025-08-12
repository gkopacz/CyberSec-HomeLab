# 🔍 Day 05 — Setup Splunk Logging & Monitoring

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Ubuntu-24.04.2%20LTS-orange?logo=ubuntu)
![SIEM](https://img.shields.io/badge/Splunk%20Enterprise-darkgreen?logo=splunk)
![Status](https://img.shields.io/badge/status-done-green)

## 🎯 Objective

With the domain infrastructure and endpoints deployed, the next core component is **centralized log visibility**. This phase focuses on provisioning a clean Ubuntu 24.04.2 LTS virtual machine in the **Monitoring subnet**, preparing it with essential tools, and laying the foundation for deploying **Splunk Enterprise** as the lab’s primary SIEM. This system will collect logs from domain controllers, clients, and network devices, enabling full-stack monitoring and future detection engineering.

## 🧠 Skills Demonstrated

- Provisioned and installed Ubuntu 24.04.2 LTS as a dedicated SIEM host
- Installed and configured **Splunk Enterprise** on Linux
- Deployed **Splunk Universal Forwarders** on Windows endpoints (DC + clients)
- Enabled forwarding of Security, System, and PowerShell logs to Splunk
- Installed and configured **Sysmon** for enriched endpoint telemetry
- Ingested and verified firewall logs from **pfSense** via remote syslog
- Validated log flows using **SPL queries** and Splunk dashboards
- Hardened visibility pipeline for future detection engineering use

# 🛠️ Setup Walkthrough

Provisioned a dedicated VM using Ubuntu 24.04.3 LTS and installed **Splunk Enterprise 9.4.3** to act as the lab’s centralized SIEM. Deployed the **Splunk Universal Forwarder** to the Domain Controller and Windows clients, then successfully ingested event logs into Splunk. Also configured **pfSense firewall log forwarding** to capture network-level telemetry alongside endpoint events.

## 1️⃣ Ubuntu Host VM Setup

### 🐧 Download Ubuntu ISO

I downloaded the latest **Ubuntu 24.04.3 LTS** desktop ISO directly from Canonical’s website: 🔗 [Download Ubuntu](https://ubuntu.com/download)

![Ubuntu_LTS](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Splunk/Ubuntu_LTS.png)

### 💻 Ubuntu VM Configuration

To serve as the **centralized monitoring node**, I spun up an Ubuntu 24.04.3 LTS virtual machine within the Monitoring subnet using Hyper-V.

| **Setting**  | **Value**                     |
|--------------|-------------------------------|
| Name         | SPLUNK                        |
| Generation   | Gen 2                         |
| CPU          | 2 vCPU                        |
| Memory       | 4 GB (Dynamic)                |
| Disk         | 80 GB (Dynamically Expanding) |
| Network      | Internal Switch (Monitoring)  |

## 2️⃣ Install Ubuntu on the VM

Once the VM was created and the ISO mounted, I proceeded with the installation of Ubuntu. From the GRUB boot menu, I selected **Try or Install Ubuntu**.

### 🌍 Language, Accessibility & Keyboard

I selected the following from the guided interactive installer.

- Selected **English** as the system language.
- Skipped accessibility options.
- Set keyboard layout to **English (US)** for both layout and variant.

### 🌐 Network Settings

Selected **wired connection** to ensure full update and driver access during install.

![Ubuntu_Network](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Splunk/Network.png)

### 💿 Install Type & Mode

Chose to **Install Ubuntu** (not “Try Ubuntu”), followed by **Interactive installation** for manual control over packages and disk config.

![Ubuntu_Install](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Splunk/Interactive.png)

### 👤 User & Host Setup

Created the user `g0bl1n` and named the machine `VM-Splunk`. Ensured password was required at login.

![Ubuntu_user](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Splunk/Account.png)

### 💽 Disk Configuration

Opted for **Erase disk and install Ubuntu**, perfect for a dedicated lab VM. No encryption was configured.

This automatically set up `/boot/efi` and `/` partitions using fat32 and EXT4.

![Ubuntu_Disk](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Splunk/Review_Install.png)

## 3️⃣ Post-Install Prep for Splunk

After rebooting into the newly installed Ubuntu system, I logged in and performed essential prep steps to ready the box for **Splunk Enterprise** installation.

### 📦 System Updates & Basic Tools

I updated all packages and installed basic dependencies using:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install wget curl net-tools -y
```

![Ubuntu_apt](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Splunk/apt_update.png)

### 📡 IP Address Management

Instead of setting a static IP manually on the Ubuntu host, I configured a **static DHCP lease** in **pfSense** to ensure the VM always receives the same IP address based on its MAC.

![Ubuntu_ifconfig](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Splunk/ip_ifconfig.png)

> 🧠 This keeps IP management centralized and avoids local misconfigurations on the VM.

## 4️⃣ Install Splunk Enterprise

With the Ubuntu VM fully configured and network-ready, I proceeded to install **Splunk Enterprise**.

I navigated to the [Splunk Enterprise](https://www.splunk.com/en_us/download/splunk-enterprise.html) web interface on my Ubuntu VM. 

I filled in the details, accepted the license agreement, and clicked **Create Account** to complete the setup.





