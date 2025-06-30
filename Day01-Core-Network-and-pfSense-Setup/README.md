# 🧱 Day 01 — Core Network & pfSense Setup

## 🎯 Objective

Set up the virtual network infrastructure using **Hyper-V** and deploy **pfSense Community Edition 2.7.2** as the core firewall to control and segment traffic between lab environments. This network layer acts as the backbone for all connectivity and security enforcement in the HomeLab. (Note: pfSense CE 2.8.0 was released after this setup. An upgrade will follow after the lab foundation is complete.)

## 🛠️ Tasks Completed

- Created and configured 5 virtual switches in Hyper-V
- Installed pfSense in a VM with 5 NICs 
- Assigned interfaces and verified connectivity
- Configured basic firewall rules and access from the host network
- Documented the setup process with screenshots and architecture diagram

## 🏗️ Setup Walkthrough

This lab runs on a **Hyper-V** virtualization platform and includes multiple **virtual machines (VMs)**, **isolated virtual switches**, and a virtualized **pfSense firewall/router** that controls traffic and enforces segmentation between networks.

To visualize the setup, I created the following network diagram:

![Network Diagram](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Network-Diagram-HomeLab.jpg)

---

Here’s a comprehensive breakdown of the lab components and a step‑by‑step walkthrough of the entire setup process.

## 1️⃣ Internet & Host PC

The **home router** connects to the internet and provides outbound access. The **Host PC** runs **Hyper-V**, serving as the base virtualization platform where all virtual machines and virtual switches reside.

> 💡  I ensured virtualization was enabled in the BIOS before starting. I preferred Hyper-V over VMware or VirtualBox because of its seamless integration with Windows and better resource management on my setup.

## 2️⃣ Virtual Switches and Subnets

The network setup begins with creating **virtual switches** in Hyper-V, which form the segmented subnets of the lab. These provide isolation and simulate real-world network zones.

| Name               | Type     | Purpose                              |
|--------------------|----------|--------------------------------------|
| WAN                | External | pfSense WAN (internet access)        |
| LAN                | Internal | Kali Linux / attacker VM             |
| Monitoring         | Internal | Splunk & logging VM network          |
| AD                 | Internal | Hosts Active Directory & Windows VMs |
| Vulnerable Machines| Internal | Vulnerable machine subnet            |

#### 🌐 Virtual Switch - WAN

This **external switch** connects pfSense to the home router, enabling internet access. It's configured in Hyper-V as an *External* type.

> 💡 I created separate virtual switches for each subnet by opening Hyper-V Manager on the host PC and using the Virtual Switch Manager under the Actions tab.

![WAN](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Virtual-Switch-WAN.png)

#### 🔒 Virtual Switch - LAN

This **internal switch** connects the Kali Linux VM (10.0.1.47). I created the remaining subnets using internal switches in the same way, but didn’t include separate screenshots for each one to avoid repetition.

![LAN](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Virtual-Switch-LAN.png)

## 3️⃣ pfSense Firewall and Router

The **pfSense firewall** serves as the central hub for routing and security within the homelab environment. Deployed as a virtual machine on **Hyper-V**, it manages traffic between the WAN and multiple internal subnets, ensuring network segmentation and control.

I started by downloading the latest pfSense ISO image from the [official website](https://www.pfsense.org/download/). <br>

At the time of setup, the current release was pfSense CE 2.7.2, as shown in the screenshot below. Since then, version 2.8.0 has been released.

> ⚠️ For new users following this guide, I recommend downloading the latest stable version available. I'll upgrade to 2.8.0 after completing the initial lab configuration.

![pfSense_ISO](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/download-pfSense-firewall-iso-image.png)

I created the **pfSense** virtual machine inside Hyper-V using the following specifications:

| Setting           | Value                          |
|-------------------|--------------------------------|
| **Name**          | pfSense                        |
| **Generation**    | Generation 2                   |
| **CPU**           | 2 vCPU                         |
| **Memory**        | 4 GB (Dynamic)                 |
| **Storage**       | 40 GB (Dynamically allocated)  |
| **Boot Firmware** | UEFI (Secure Boot disabled)    |
| **Network Adapters** | 1 (initially) — WAN         |

> 💡 I chose Generation 2 to support UEFI boot and modern compatibility. Secure Boot must be disabled for pfSense to boot properly.

After creating the VM:
- I disabled **Secure Boot**
- Set the **boot order** (as seen below)
- Added the remaining **network adapters** for LAN and internal subnets

![pfSense_Settings](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/pfSense-VM-Settings.png)

Upon initial boot, pfSense prompts for instalation and partitioning the disk. I made sure to partition the disk with the following settings.

* AUTO (ZFS) - Guided Root-on-ZFS
* Stripe - No Redundancy

After reboot, pfSense prompts for interface assignments.

> 💡 When asked if VLANs need to be set up first, press n.

Next I manually setup the following interfaces.

* WAN = hn0
* LAN = hn1
* OPT1 = hn2 (Monitoring)
* OPT2 = hn3 (AD)
* OPT3 = hn4 (Vulnerable Machines)

*Ref 6: pfSense interfaces*

![pfSense_interfaces](https://github.com/gaman547/CyberSec-HomeLab/blob/main/images/pfSense-interface-config.png)

Each interface is configured with a static IP address appropriate for its subnet. The WAN interface got the IP address from my home router network.
The Default LAN ip addres is 192.168.1.1/24 while the other interfaces are not configured yet.

Next I selected option 2 as seen bellow.

*Ref 7: pfSense LAN setup*

![pfSense_LAN_Setup](https://github.com/gaman547/CyberSec-HomeLab/blob/main/images/pfSense-LAN-setup.png)

Afterwards I selected the LAN interface and configured it as follows.

* Configure IPv4 address LAN interface via DHCP? = n
* Enter the new LAN IPv4 address = 10.0.1.1
* Enter the new LAN IPv4 subnet bit count = 24

> 💡 Press Enter as we do not want any upstream gateway for LAN interface.

*Ref 8: pfSense LAN interface configuration* 

![pfSense_LAN_interface](https://github.com/gaman547/CyberSec-HomeLab/blob/main/images/pfSense-LAN-interface.png)

I continued with the following configuration.

* Configure IPv6 address LAN interface via DHCP6 = n
* For the new LAN IPv6 address question = press ENTER
* Do you want to enable DHCP server on LAN? = y
* Enter the start address of the IPv4 clint address range = 10.0.1.100
* Enter the end address of the IPv4 client address range = 10.0.1.200
* Do you want to revert to HTTP as the webConfigurator protocol = n

*Ref 9: pfSense LAN iterface DHCP configuration*

![pfSense_LAN_interface_DHCP](https://github.com/gaman547/CyberSec-HomeLab/blob/main/images/pfSense-LAN-interface-dhcp.png)

I followed the same patern for Monitoring and Vulnerable Machines interfaces. 

> 💡 The only difference for the AD interface is that I did not enable DHCP service. The Active Directory Domain Controller will be responsible to assign IP addresses to the machines in the AD network. 

Below would be the interface IP addresses look like.

*Ref 10: pfSense final interface check*

![pfSense_interfaces_final](https://github.com/gaman547/CyberSec-HomeLab/blob/main/images/pfSense-interfaces-final-check.png)





## 🔧 pfSense Configuration

- **Version:** pfSense CE 2.8.0 :contentReference[oaicite:1]{index=1}  
- **Interfaces:**
  - **WAN** → External switch
  - **LAN** → LAN switch
- **Initial setup steps:**
  - Set admin password and hostname
  - Assigned interfaces and configured static IPs
  - Enabled web GUI access on LAN
- **Firewall Rules:**
  - Allow ICMP from LAN to WAN
  - Allow web GUI access (HTTPS) from LAN on 443
  - Block all unauthorized inbound WAN traffic
