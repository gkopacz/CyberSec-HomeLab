# 🔍 Day 05 — Setup Splunk Logging & Monitoring

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Ubuntu-24.04.2%20LTS-orange?logo=ubuntu)
![SIEM](https://img.shields.io/badge/Splunk%20Enterprise-9.4.3-darkgreen?logo=splunk)
![Status](https://img.shields.io/badge/status-done-green)



## 🎯 Objective

## 🧠 Skills Demonstrated

# 🛠️ Setup Walkthrough

With the domain infrastructure and endpoints deployed, the next core component is **centralized log visibility**. This phase focuses on provisioning a clean Ubuntu 24.04.2 LTS virtual machine in the **Monitoring subnet**, preparing it with essential tools, and laying the foundation for deploying **Splunk Enterprise 9.4.3** as the lab’s primary SIEM. This system will collect logs from domain controllers, clients, and network devices, enabling full-stack monitoring and future detection engineering.

## 1️⃣ Ubuntu Host VM Setup

To serve as the **centralized monitoring node**, I spun up an Ubuntu 24.04.2 LTS virtual machine within the Monitoring subnet using Hyper-V.

| **Setting**  | **Value**                     |
|--------------|-------------------------------|
| Name         | MON-SPLUNK01                  |
| Generation   | Gen 2                         |
| CPU          | 2 vCPU                        |
| Memory       | 4 GB (Dynamic)                |
| Disk         | 100 GB                        |
| Network      | Internal Switch (Monitoring)  |

### 🐧 Download Ubuntu ISO

I downloaded the latest **Ubuntu 24.04.2 LTS** desktop ISO directly from Canonical’s website: 🔗 [Download Ubuntu](https://ubuntu.com/download)

> 💡 LTS = Long Term Support → 5 years of updates (2024–2029)

## 2️⃣ Install Ubuntu on the VM

From the GRUB boot menu, I selected **Try or Install Ubuntu**.

### 🌐 Language, Keyboard & Network

I selected the following from the guided interactive installer.

- Language: **English**  
- Keyboard: **English (US)**  
- Internet: **Wired connection (recommended)**

### ⚙️ Installation Type

Chose **Interactive installation** for full control.


Selected **Default application set** (essentials only) and checked the box for third-party drivers.

### 💽 Disk Configuration

Opted for automatic partitioning: **Erase disk and install Ubuntu**.


On the review screen, I confirmed the layout with `/boot/efi` and `/` partitions.

### 🌍 Timezone & Region

> ✅ After installation, I clicked **Restart Now** and booted into the new system.

## 3️⃣ Post-Install Prep for Splunk

After rebooting into the newly installed Ubuntu system, I logged in and performed essential prep steps to ready the box for **Splunk Enterprise** installation.

### 📦 System Updates & Basic Tools

I updated all packages and installed basic dependencies using:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install wget curl net-tools -y
```

> 🧠 net-tools includes ifconfig, which is useful for quick IP checks.


