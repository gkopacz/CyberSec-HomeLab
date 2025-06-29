# 🛡️ Cybersecurity HomeLab 🛡️

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![Firewall](https://img.shields.io/badge/firewall-pfSense-red?logo=pfsense)
![Monitoring](https://img.shields.io/badge/monitoring-Splunk-black?logo=splunk)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

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

| Tool              | Purpose                                 |
| ----------------- | --------------------------------------- |
| Hyper-V Manager   | Virtualization and VM Management        |
| pfSense           | Firewall & Network Routing              |
| Splunk Enterprise | Centralized Log Management & Monitoring |
| Microsoft Visio   | Network Diagram Design                  |

## 🏗️ What We'll Build

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

* Kali Linux - Used for testing, scanning, and simulated attacks.
* Monitoring VM - Runs tools like Splunk, Sysmon, Wireshark — core for blue team analysis.
* Windows Server 2019 - Domain Controller with DNS and AD services.
* Windows Clients (10/11) - Domain-joined endpoints to simulate a corporate environment.
* Metasploitable / VulnHub Machines - Vulnerable-by-design VMs for testing detection and response.

## 🌐 Network Diagram

I've created the following network diagram to have a better understanding of the structure and functionality.

*Ref 1: Network Diagram*

![Network Diagram](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Network-Diagram-HomeLab.jpg)

✅ Next Step

Proceed to Day 01 to see how to set up *Virtual Switches* in *Hyper-V* and *pfSense* Setup.

## 📂 Project Structure 

| Day | Title                                                                 | Link                                                                 |
|-----|-----------------------------------------------------------------------|----------------------------------------------------------------------|
| 01  | Network Infrastructure and pfSense Setup                              | [Go to Day 01](./Day01-Network-Infrastructure-and-pfSense-Setup/)   |
| 02  | Kali Linux VM Setup                                                   | [Go to Day 02](./Day02-Kali-VM-Setup/)                              |
| 03  | Active Directory Setup and Windows Host Domain Join                   | [Go to Day 03](./Day03-Active-Directory-Setup-and-Windows-Host-Domain-Join/) |
| 04  | Splunk Monitoring Setup                                               | [Go to Day 04](./Day04-Splunk-Monitoring-Setup/)                    |
| 05  | Vulnerable VMs Deployment                                             | [Go to Day 05](./Day05-Vulnerable-VMs-Deployment/)                  |
