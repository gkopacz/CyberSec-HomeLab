# 🗄️ Day 03 — Active Directory Deployment & Domain Join

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Windows%20Server-2019-lightgrey?logo=windows)
![Client1](https://img.shields.io/badge/Windows%2010-blue?logo=windows)
![Client2](https://img.shields.io/badge/Windows%2011-blueviolet?logo=windows)
![Status](https://img.shields.io/badge/status-in--progress-yellow)

## 🎯 Objective

Deploy a **Windows Server 2019** VM in the `AD` subnet and promote it to a **Domain Controller**. Set up **Active Directory**, configure **DNS**, and enable **DHCP** for the subnet. Join Windows clients to the new domain to validate identity management and directory-based authentication. This lays the foundation for centralized access control, Group Policy testing, and future detection engineering scenarios.


## 🧠 Skills Demonstrated

- Deployment of **Windows Server 2019** on Hyper-V with Gen 2 settings  
- Promotion of standalone server to **Active Directory Domain Services (AD DS)**  
- Configuration of **DNS Server** for internal name resolution  
- Setup of **DHCP Server** for automated IP assignment in the AD zone  
- Creation of an **Active Directory forest and domain** (e.g., `adlab.local`)  
- Domain join of Windows 10 and 11 clients and login validation  
- Basic troubleshooting of domain-related connectivity and authentication issues

# 🛠️ Setup Walkthrough

The Windows Server 2019 machine will serve as the backbone of the Active Directory (AD) zone in my lab. Deployed as a virtual machine on Hyper-V, it will be promoted to a Domain Controller, provide DNS and DHCP (for the AD subnet), and eventually manage user/group policies and domain authentication for Windows clients.

I started by downloading the Windows Server 2019 ISO from the official Microsoft Evaluation Center: 🔗 [Download Windows Server 2019](https://www.microsoft.com/en-in/evalcenter/evaluate-windows-server-2019)

## 1️⃣ Create the Windows Server VM

In **Hyper-V Manager**, I created a new Gen 2 virtual machine:

| Setting       | Value                           |
|---------------|---------------------------------|
| **Name**      | DC01-WinServer2019              |
| **Generation**| Gen 2 (UEFI, Secure Boot off)   |
| **CPU**       | 2 vCPU                          |
| **Memory**    | 4 GB (Dynamic)                  |
| **Disk**      | 60 GB (Dynamically Expanding)   |
| **NIC**       | Internal switch (AD subnet)     |

## 2️⃣ Installing Windows Server 2019

I mounted the **Windows Server 2019 ISO** in the virtual DVD drive of the Hyper‑V VM and powered it on.  

To ensure a smooth install, I reviewed and adjusted the **boot order** in the VM settings:

1. **DVD Drive** (for ISO boot)
2. **Hard Drive** (for post-install boots)
3. **Network Adapter** (PXE boot — left last)

> 💡 Setting the correct boot sequence avoids boot loops and ensures the OS installer launches on first boot. Once installation is complete, I’ll remove the **DVD drive** from the VM to prevent accidental reboots into the installer.

### 🗣️ Language & Keyboard

Selected the default options:

  * Language: English (United States)
  * Time and currency: English (United States)
  * Keyboard: US

### 📦 Choose Edition

During setup, I was prompted to select the OS edition. I went with: `Windows Server 2019 Datacenter Evaluation (Desktop Experience)`

![Choose Edition](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-OS-version.png)

Why Datacenter❓

- It's better optimized for **Hyper-V environments**.
- It includes advanced features like **Shielded VMs**, **Storage Spaces Direct**, and **Software-Defined Networking**, which may come in handy in future lab phases.

> 📚 For a homelab, either edition works — but Datacenter ensures maximum compatibility with virtualization tasks and gives you flexibility down the line.
> Microsoft provides a full breakdown of **Standard vs Datacenter** features [here](https://learn.microsoft.com/en-us/windows-server/get-started/editions-comparison?pivots=windows-server-2019&tabs=full-comparison).

### 🔧 Install Type

I selected **Custom: Install Windows only (Advanced)** to perform a clean, manual installation.

This option allowed me to partition the virtual disk from scratch and ensured no pre-installed roles or features were added — ideal for a domain controller build where I want full control over each configuration step.

### 💽 Disk Partition

During setup, I clicked **New** and allocated the entire **60 GB** of virtual disk space to a single **primary partition**, then hit **Apply**.

![Disk_Partition_size](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-Disk-Partition.png)

Once the partition table was created, I selected the primary partition and continued with installation.

![Disk_Partition](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-Disk-Partition-done.png)

### 🔑 Administrator Password Setup

After installation completed, I was prompted to set a password for the **built-in Administrator** account.

I chose a strong, complex password that meets Active Directory best practices.

## 3️⃣ Initial Network Configuration

After logging in to the newly installed **Windows Server 2019** VM, I launched **Server Manager** to perform initial configuration steps.

### 🖥️ Rename the Host

By default, Windows assigns a random hostname. I renamed it to something meaningful:

- **Old Name:** WIN-XXXXXXXXXX  
- **New Name:** `DC01`

To rename the server, I opened Server Manager, clicked on Local Server, and selected the Computer Name field. From there, I hit Change, entered `DC01` as the new name, and rebooted when prompted.

![Hostname](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-hostname.png)

### 🌐 Configure Static IP Address

Since I planned to handle DHCP at the Windows Server level, I didn’t configure DHCP on pfSense for the AD subnet. Instead, I manually assigned a static IP address to the domain controller during initial setup to ensure stability for DNS and future domain operations.

Steps:
1. Go to **Control Panel** → **Network and Sharing Center**
2. Click **Change adapter settings**
3. Right-click **Ethernet adapter** → **Properties**
4. Select **Internet Protocol Version 4 (TCP/IPv4)** → **Properties**
5. Configure:
   - IP Address: `10.0.3.9`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: `10.0.3.1` 
   - Preferred DNS: `10.0.3.9` (this server will run DNS after domain promotion)

![IP_Address](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-static-ip.png)

> 💡 Assigning a static IP ensures domain services remain accessible and clients can reliably resolve and contact the domain controller.

## 4️⃣ Install AD DS, DNS & DHCP Roles

I launched **Server Manager**, then navigated to:

**Start Menu** → **Server Manager** → **Manage** → **Add Roles and Features**

From there, I followed these steps:

1. **Installation Type:** Chose `Role-based or feature-based installation` and clicked **Next**
2. **Server Selection:** Confirmed `Select a server from the server pool` was selected — my server (`DC01`) was auto-selected
3. **Server Roles:** Enabled the following:
   - **Active Directory Domain Services (AD DS)**
   - **DNS Server**
   - **DHCP Server**
  
![Server_Roles](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-server-roles.png)

> 💡 This preps the server with the necessary components to become a fully functional **Domain Controller**.

### 🏰 Promote the Server to Domain Controller

Once the roles were installed, I went back to **Server Manager** and clicked the **flag icon** in the top-right corner. From there, I selected **Promote this server to a domain controller**.

Here’s how I handled the wizard:

### 🌲 **Deployment Configuration:**  

   Selected `Add a new forest` and set the **root domain name** to `adlab.local`

![Promoe_DC](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-promote-dc.png)

### 🔐 **Domain Controller Options:**  

   Kept the default selections (Domain Name System (DNS) server, Global Catalog, etc.)  
   Set a strong **DSRM (Directory Services Restore Mode)** password

![DSRM_PWD](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-dsrm-pwd.png)

### 🧭 **DNS Options:**  

   Left everything as default and hit **Next**

### 📂 **Additional Pages:**  

   Accepted the default **NetBIOS name**, **paths**, and skipped any extra configuration

### ✅ **Prerequisites Check:**  

   Waited for the check to complete successfully, then clicked **Install**

![PRE_CHECK](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-pre-check.png)

> 💡 After completing the wizard, the system automatically rebooted. Once it came back online, the server was now an official **Domain Controller** for `adlab.local`.

## 5️⃣ Configure DNS on the Domain Controller

After the domain controller promotion, the DNS Server role was already installed and active.

To make sure DNS name resolution works beyond the domain (e.g. internet access), I configured DNS Forwarders to point upstream to the pfSense gateway.

### 📡 Why Use Forwarders Instead of Forward Lookup Zones

In this setup, my Domain Controller already hosts the `adlab.local` **Forward Lookup Zone**, which handles internal name resolution (e.g. `dc01.adlab.local`).

However, it can’t resolve **external DNS queries** (like `github.com` or `kali.org`) unless I configure it to forward those requests.

That’s why I used the **Forwarders tab** — to point the DC’s DNS to pfSense (`10.0.3.1`) as its upstream resolver. From there, pfSense can resolve external domains on behalf of the internal network.

| Feature              | **Forwarders**                                                                                       | **Forward Lookup Zones**                                                                 |
|----------------------|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Purpose**          | Forwards queries your DNS server can't resolve locally to another DNS server (like `10.0.3.1`).      | Stores domain-to-IP mappings your DNS server manages directly.                           |
| **Use Case**         | When you want your DC to forward unresolved DNS queries (e.g., external domains) to an upstream DNS. | When you want your DC to host and manage records for internal domains (e.g., `adlab.local`). |
| **Example**          | `google.com` not found locally → forward to pfSense → pfSense resolves it.                           | You create records for `labvm01.adlab.local`, etc.                                        |

> 🧠 Think of **Forwarders** as a “fallback route” for DNS — when your DC doesn’t know an answer, it asks pfSense.

### 🧭 Configuration Steps

I launched **Server Manager**, clicked **Tools** → **DNS** to open the DNS Manager console.

Inside **DNS Manager**:
- I expanded the tree and right-clicked my domain controller (`DC01`)
- Selected **Properties**
- Navigated to the **Forwarders** tab

There, I:
- Clicked **Edit**
- Added my pfSense LAN IP: `10.0.3.1`
- Clicked **OK** to save the configuration

![DNS](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-dns.png)

> 💡 Without this, internal clients could resolve only domain names (e.g., adlab.local) but wouldn’t be able to browse the web or resolve public domains like microsoft.com, which is critical for things like Windows Updates and pulling packages.

## 6️⃣ Configure DHCP on the Domain Controller

With the Domain Controller promoted and DNS forwarders configured, I proceeded to configure the **DHCP Server** to manage dynamic IP assignment for the AD subnet (`10.0.3.0/24`).

> 💡 On pfSense: make sure DHCP is disabled for the AD interface to avoid conflict

### ⚙️ DHCP Post-Deployment Configuration

Since the **DHCP Server role** was already installed, I clicked the **flag icon** in **Server Manager** to launch the **post-deployment configuration**.

![DHCP](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-complete-dhcp.png)

In the Authorization step, I selected: **Use the following user’s credentials:** `ADLAB\Administrator`. Then I clicked **Commit**, followed by **Close**.

> 💡 DHCP must be authorized in Active Directory so it can lease IP addresses on behalf of the domain.

![DHCP_cred](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-dhcp-cred.png)

> 🔐 In production environments, this step is often completed using a **service account** — a special low-privilege account dedicated to running background services. It’s a best practice that improves auditability and limits exposure if credentials are compromised. For simplicity, I used the built-in Administrator account.

### 📦 Create DHCP Scope for AD Subnet

I opened **Server Manager** → **Tools** → **DHCP**, expanded the server tree (`DC01.adlab.local`), right-clicked **IPv4**, and selected **New Scope** to launch the wizard:

- **Scope Name:** `AD_LAB_SCOPE`  
- **Description:** DHCP scope for AD subnet

![DHCP_scope](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-dhcp-scope-name.png)

#### IP Address Range:

- **Start IP:** `10.0.3.100`  
- **End IP:** `10.0.3.200`  
- **Length:** `24` → This auto-set the **Subnet Mask** to `255.255.255.0`

![DHCP_range](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-dhcp-range.png)

#### Exclusions & Delay:

- Skipped — no exclusions needed between `.100` and `.200`

#### Lease Duration:

- Left the default at **8 days**

![DHCP_lease](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-dhcp-lease.png)

> 💡 In production environments, DHCP lease time is often set to **24 hours** for better IP reuse and tighter control. In this lab setup, 8 days is totally fine, fewer clients, more stability.

### 🛠️ Configure DHCP Options

I selected: **Yes, I want to configure these options now**

- **Router (Default Gateway):**
  - IP: `10.0.3.1`
  - Clicked **Add**

![DHCP_dgw](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-dhcp-defaultgw.png)

- **Domain Name and DNS Servers:**
  - Confirmed domain: `adlab.local`
  - Confirmed DNS Server: `10.0.3.9` (the DC itself)
 
![DHCP_DNS](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-dhcp-dns.png)

> 🧠 This is crucial — domain clients must resolve DNS through the **Domain Controller** in order to join the domain and access network services.

- **WINS Server:** Skipped (not required in modern Windows networks)

- **Activate Scope:** Selected **Yes, I want to activate this scope now**

> 📡 **With the DHCP scope live**, any Windows clients on the AD subnet will now automatically receive an IP address, gateway, and DNS settings — all necessary for smooth domain joins and centralized management.

## 7️⃣ Active Directory Certificate Services (AD CS) Configuration

With the core AD infrastructure online, I proceeded to install and configure **Active Directory Certificate Services (AD CS)** to enable future features like secure authentication, encrypted communications, and domain certificate auto-enrollment. This adds a local Certification Authority (CA), which can issue certificates for domain-joined machines and services.

This step is optional for basic AD environments but adds realism for future scenarios like:
 * Deploying enterprise certificates
 * Testing certificate-based authentication
 * Signing internal services with trusted certificates
 * Certificate auto-enrollment via GPO
 * HTTPS with internal certs
 * encrypted RDP connections
 
### 🛠️ Installing the AD CS Role

From **Server Manager**, I launched the **Add Roles and Features Wizard**:

1. Pressed **Start Menu** → **Server Manager** → **Manage** → **Add Roles and Features**
2. Selected **Role-based or feature-based installation**
3. Confirmed **DC01** as the target server from the pool
4. In **Server Roles**, I selected: `Active Directory Certificate Services`
5. In **AD CS Role Services**, I selected: `Certification Authority`
6. Then I continued through the wizard and hit **Install**.   

![Cert_Serv](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-cert-serv.png)

### ⚙️ AD CS Post-Install Configuration

After installation, I clicked the **flag icon** in Server Manager and selected **"Configure Active Directory Certificate Services on the destination server."**

On the Credentials screen, I verified that I was using the domain admin account: `ADLAB\Administrator` 

![Cert_Cred](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-cert-cred.png)

### 🏢 Role Service Configuration

I selected `Certification Authority` as the only role service. This ensures we’re just standing up a simple Certificate Authority for now.

![Cert_Auth](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-cert-auth.png)

### 🏛️ Setup Type → Enterprise CA

I selected `Enterprise CA` as seen in the screenshot below.

![Enterprise_CA](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-cert-enterprisa-ca.png)

> 🧠 An Enterprise CA is integrated with Active Directory, meaning it can automatically issue certificates to authenticated domain members based on Group Policy or request templates. This is ideal for lab environments simulating corporate networks.

### 🧬 CA Type → Root CA

Next, I chose: `Root CA` as seen below.

![Root_CA](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-cert-root-ca.png)

> 🔐 A Root CA is the top of the certificate chain — trusted by all domain clients. Since this is the first CA in the lab, making it a root authority is necessary to establish internal trust.

### 📜 Finish Configuration

I proceeded through the remaining pages using the default settings:
- Created a **new private key** (since this is the first CA)
- Left default cryptographic settings (RSA 2048, SHA256)
- Set a **common name** for the CA: `ADLAB-CA`
- Accepted the default validity period (5 years)
- Confirmed default storage paths for the certificate database and logs
- Completed the wizard and let the system configure the CA

![Cert_conf](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-cert-config.png)

## 8️⃣ Populate Active Directory: Create Dummy Users & Organizational Units

With the domain controller, DNS and DHCP configured, I moved on to creating a few test users to simulate a real-world Active Directory environment.

These accounts will later help me validate **domain joins**, **authentication flows**, **GPO enforcement**, and even simulate attacks or detection rules.

Why Create Dummy Users❓

In enterprise environments, user accounts form the core of identity-based security. Even in a lab, having realistic users allows me to test authentication logging, group policies, privilege escalation paths, and more.

Why Not Use Default Containers❓

By default, Active Directory provides built-in **containers** like `Users` and `Computers`. However, these aren’t actual OUs and **don’t support GPO linking or delegation**.

To follow best practices, I created custom OUs:

- `OU=LabUsers,DC=adlab,DC=local`
- `OU=LabAdmins,DC=adlab,DC=local`
- `OU=LabComputers,DC=adlab,DC=local`

This structure gives me control and flexibility as the lab grows.

### 👥 Creating Organisational Units (OU)

1. Open **Active Directory Users and Computers**
2. Right-click the domain (`adlab.local`) → **New** → **Organizational Unit**
3. Enter name (e.g., `LabUsers`)
4. Repeat for each desired category

![AD_OU](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-ad-ou.png)

### 👤 Creating Test Accounts

By default, any new user account is placed in the `Users` container (`CN=Users`), which is a **container object**, *not* an Organizational Unit (OU).

You can either move them manually after creation, or create them directly inside a specific OU:

1. Navigate to `LabUsers` OU → Right-click → **New** → **User**
2. Provide full name and username
3. Assign a strong password
4. Uncheck: `User must change password at next logon`
5. Check: `Password never expires`

![AD_User](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-ad-newuser.png)

> 🔐 I may enforce password complexity and expiration later using Group Policy. For now, I'm disabling expiration to keep testing consistent and simple.

### 🧪 Sample Accounts

| Name         | Username       | OU          | Role             | Notes                   |
|--------------|----------------|-------------|------------------|-------------------------|
| Alice Smith  | `alice.smith`  | LabUsers    | Standard User    | Password never expires  |
| Bob Johnson  | `bob.johnson`  | LabUsers    | Standard User    | Password never expires  |
| Admin Test   | `admin.test`   | LabAdmins   | Privileged User  | For GPO & escalation    |

## 9️⃣ Windows Client Setup & Domain Join

> Bringing Windows 10 and 11 machines into the AD domain to simulate real-world user environments, policy propagation, and security enforcement.

I spun up a **Windows 10 Pro** VM in Hyper-V:

- Assigned NIC to the AD internal switch
- Installed updates & renamed machine
- Opened `System Properties` → `Domain Join`
- Joined `adlab.local` using AD credentials
- Rebooted and logged in as domain user

## ✅ Validation

- `ipconfig /all` shows DNS = DC IP
- `ping dc.adlab.local` resolves successfully
- Logged in with domain credentials
- Accessed `Active Directory Users & Computers`
- Created test user & OU structure

## 🔜 Next Step

On Day 04, I’ll configure Splunk and begin sending Windows logs from the DC and clients. This will kick off the **detection engineering** and centralized monitoring phase of the HomeLab.

