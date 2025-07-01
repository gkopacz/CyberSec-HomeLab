# 🛡️ Cybersecurity HomeLab 🛡️

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![Firewall](https://img.shields.io/badge/firewall-pfSense-red?logo=pfsense)
![Logs](https://img.shields.io/badge/logs-Splunk-black?logo=splunk)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT) <br>
![Status](https://img.shields.io/badge/status-in--progress-yellow)
![Last Commit](https://img.shields.io/github/last-commit/gkopacz/CyberSec-HomeLab)
![Visitors](https://visitor-badge.laobi.icu/badge?page_id=gkopacz/CyberSec-HomeLab)

## 🎯 Objective

Design and implement a **cybersecurity homelab** tailored for learning, testing, and experimenting in cybersecurity, networking, and system administration, while ensuring a secure and isolated environment.

> 🗓️ This is the first major project in my 90-Day Cybersecurity Lab Challenge, focused on building hands-on, blue team skills from scratch.

## 🧠 Skills Showcased

### 🖥️ Virtualization 
* Proficient in setting up and managing virtual machines using **Hyper-V**.
* Optimized VM performance through effective allocation of **CPU, memory, and storage**.
  
### 🌐 Networking 
* **Subnetting**: Designed and deployed multiple subnets.
* **Routing**: Configured **pfSense** to route traffic between subnets and external networks.
* **Firewall Management**: Defined firewall rules to control traffic flow and ensure segmentation.
* **Network Isolation**: Achieved secure communication using isolated virtual switches.

### 🧑‍💻 System Administration 
* Installed and managed **Windows Server 2019**, Windows 10 clients, and **Linux** machines.
* Configured **Active Directory Domain Services (AD DS)**:
  * Set up a Domain Controller.
  * Managed users, groups, and permissions.
  * Deployed **DNS** and **AD Certificate Services**.
  * Joined client machines to the domain.

### 📊 Monitoring & Logging
* Built a dedicated **monitoring VM** for:
  * Network traffic analysis
  * Log collection
  * System health monitoring
* Installed and configured **Splunk Enterprise** for log ingestion and alerting.
  
### 📝 Documentation and Design
* Created a detailed **network architecture diagram** using **Microsoft Visio**.
* Documented the entire setup process to enable easy replication.

## 🧰 Tools Used

| Tool              | Purpose                                           |
|-------------------|---------------------------------------------------|
| Hyper-V Manager   | Virtualization and VM Management                  |
| pfSense           | Firewall & Network Routing                        |
| Splunk Enterprise | Centralized Log Management & Alerting             |
| Sysmon            | Endpoint Telemetry via Windows Event Logging      |
| Wireshark         | Packet Capture & Traffic Analysis                 |
| Microsoft Visio   | Network Diagram Design                            |

## 📂 Project Structure 

| Day | Title                                           | Link                                                                 | Status          |
|-----|-------------------------------------------------|----------------------------------------------------------------------|-----------------|
| 01  | Core Network & pfSense Setup                    | [Go to Day 01](https://github.com/gkopacz/CyberSec-HomeLab/tree/main/Day01-Core-Network-and-pfSense-Setup) | ✅ Done         |
| 02  | Kali Linux VM Setup & pfSense Firewall Rules    | [Go to Day 02](https://github.com/gkopacz/CyberSec-HomeLab/tree/main/Day02-Kali-Linux-VM-Setup-and-pfSense-Configuration) | 🛠️ In Progress |
| 03  | AD Setup + Domain Join                          | [Go to Day 03](https://github.com/gkopacz/CyberSec-HomeLab/tree/main/Day03-AD-Setup-and-Domain-Join) | ⏳ Pending      |
| 04  | Splunk Logging & Monitoring                     | [Go to Day 04](https://github.com/gkopacz/CyberSec-HomeLab/tree/main/Day04-Splunk-Logging-and-Monitoring) | ⏳ Pending      |
| 05  | Deploy Vulnerable Machines                      | [Go to Day 05](https://github.com/gkopacz/CyberSec-HomeLab/tree/main/Day05-Deploy-Vulnerable-Machines) | ⏳ Pending      |

## 🏗️ Project Overview

This lab simulates a segmented, enterprise-style network designed for security operations, monitoring, and attack simulation. 

It’s built on a Hyper-V virtualization platform and features multiple virtual machines (VMs), isolated virtual switches, and a virtualized pfSense firewall to manage traffic flow and enforce strict network segmentation.

### 🔐 Core Infrastructure

* pfSense Firewall (Virtualized)
  * Central gateway and router controlling traffic between internal subnets and outbound internet.    
* Hyper-V Virtual Switches
  * Segmented into dedicated VLAN-like zones:
    * WAN — Internet access from the host network
    * LAN — Used by attacker VM (Kali)
    * Monitoring — Hosts Splunk and other log collectors
    * AD — Contains domain controller and domain-joined Windows hosts
    * Vulnerable — Isolated subnet for intentionally exploitable VMs

### 🧩 VM Roles

* Kali Linux — Used for testing, scanning, and simulated attacks.
* Monitoring VM — Runs tools like Splunk, Sysmon, Wireshark — core for blue team analysis.
* Windows Server 2019 — Domain Controller with DNS and AD services.
* Windows Clients (10/11) — Domain-joined endpoints to simulate a corporate environment.
* Metasploitable / VulnHub Machines — Vulnerable-by-design VMs for testing detection and response.

## 🌐 Network Diagram

I've created the following network diagram to have a better understanding of the structure and functionality.

*Ref 1: Network Diagram*

![Network Diagram](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Network-Diagram-HomeLab.jpg)

### ✅ Next Step

The lab begins with [Day 01](https://github.com/gkopacz/CyberSec-HomeLab/tree/main/Day01-Core-Network-and-pfSense-Setup), where I configured the virtual switches and set up pfSense as the core firewall.
