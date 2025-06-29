# 🛡️ Cybersecurity HomeLab 🛡️

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![Firewall](https://img.shields.io/badge/firewall-pfSense-red?logo=pfsense)
![Monitoring](https://img.shields.io/badge/monitoring-Splunk-black?logo=splunk)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🎯 Objective

> 🗓️ This is the first major project in my 90-Day Cybersecurity Lab Challenge, focused on building hands-on, blue team skills from scratch.

Design and implement a **cybersecurity homelab** tailored for learning, testing, and experimenting in cybersecurity, networking, and system administration, while ensuring a secure and isolated environment.

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

## Placeholder

## 🌐 Network Diagram

I've created the following network diagram to have a better understanding of the structure and functionality.

*Ref 1: Network Diagram*

![Network Diagram](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/Network-Diagram-HomeLab.jpg)

## 🗂️ Lab Build Timeline

| Day | Title                                                                 | Link                                                                 |
|-----|-----------------------------------------------------------------------|----------------------------------------------------------------------|
| 01  | Network Infrastructure and pfSense Setup                              | [Go to Day 01](./Day01-Network-Infrastructure-and-pfSense-Setup/)   |
| 02  | Kali Linux VM Setup                                                   | [Go to Day 02](./Day02-Kali-VM-Setup/)                              |
| 03  | Active Directory Setup and Windows Host Domain Join                   | [Go to Day 03](./Day03-Active-Directory-Setup-and-Windows-Host-Domain-Join/) |
| 04  | Splunk Monitoring Setup                                               | [Go to Day 04](./Day04-Splunk-Monitoring-Setup/)                    |
| 05  | Vulnerable VMs Deployment                                             | [Go to Day 05](./Day05-Vulnerable-VMs-Deployment/)                  |
