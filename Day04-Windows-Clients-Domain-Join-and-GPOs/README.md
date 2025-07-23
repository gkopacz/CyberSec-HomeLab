# 🖥️ Day 04 — Windows Clients Domain Join & GPOs

![Platform](https://img.shields.io/badge/platform-HyperV-blue?logo=windows)
![OS](https://img.shields.io/badge/Windows%20Server-2019-lightgrey?logo=windows)
![Client1](https://img.shields.io/badge/Windows%2010-blue?logo=windows)
![Client2](https://img.shields.io/badge/Windows%2011-blueviolet?logo=windows)
![Status](https://img.shields.io/badge/status-done-green)

## 🎯 Objective

Join Windows 10 and 11 endpoints to the adlab.local domain, validate centralized authentication, and deploy foundational Group Policy Objects (GPOs) to enforce password policies, remote access settings, and audit logging. This strengthens domain-based access control and prepares the environment for future log monitoring and detection engineering.

## 🧠 Skills Demonstrated

- Domain join of Windows 10 and 11 clients to Active Directory (`adlab.local`)  
- Verification of domain-based authentication using test accounts  
- Group Policy Object (GPO) creation, linking, and scope targeting  
- Configuration of user rights for Remote Desktop access via GPO  
- Enforcement of password expiration and complexity policies  
- Deployment of advanced audit policies for authentication and directory access  
- Organizational Unit (OU) targeting for granular policy control  
- Structured testing of login behavior and GPO impact across clients

# 🛠️ Setup Walkthrough

With the domain controller, DNS, and DHCP services now fully configured, I moved on to provisioning two Windows client machines, one **Windows 10** and one **Windows 11** - and joining them to the `adlab.local` domain. This included renaming them to match lab conventions, and verifying domain authentication using the test user accounts created earlier.

## 1️⃣ Windows Clients VM Setup

These endpoints simulate real-world users in a corporate network and allow testing of:

- 🛂 **Domain authentication**
- 🧠 **DNS functionality**
- 🔁 **DHCP scope delivery**
- 🔐 **Account & policy enforcement**

### 📥 Download Windows official ISO

To begin, I downloaded the Windows 10 and 11 ISO directly from Microsoft’s official Evaluation Center: 🔗 [Download Windows 10](https://www.microsoft.com/en-us/evalcenter/)

For each OS, I selected the appropriate **Enterprise edition** from the dropdown menu, filled out the required form, and downloaded the **64-bit version** in **English**.

### 💻 Windows VM Configuration

I spun up two Hyper-V virtual machines with the following settings:

| **Setting**  | **Value**                      |
|--------------|--------------------------------|
| Name         | Win10-Client, Win11-Client     |
| Generation   | Gen 2                          |
| CPU          | 2 vCPU                         |
| Memory       | 4 GB (Dynamic)                 |
| Disk         | 50 GB                          |
| Network      | Internal Switch (AD subnet)    |

## 2️⃣ Install Windows on the VMs

On the initial setup screen, I selected the default **language**, **time and currency format**, and **keyboard layout**, then clicked **Next** followed by **Install now**.

![Win10_language](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-lang.png)

When asked for a license key, I clicked **I don’t have a product key** to proceed without activation.

![Win10_license](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-license.png)

I was then presented with a list of Windows editions. I selected **Windows 10 Pro (x64)** and clicked **Next**.

![Win10_version](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-version.png)

After accepting the license terms, I selected **Custom: Install Windows only (advanced)** as the installation method. 

![Win10_custom](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-custom.png)

I created a new partition, allocated the full disk size, confirmed the prompt, and proceeded with the installation.

![Win10_partition](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-partition.png)

Once the OS installation completed, the system rebooted and began the Out-of-Box Experience (OOBE) setup.

I was asked how I wanted to set up the device — I selected **Set up for an organization** and clicked **Next**.

![Win10_join_org](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-org.png)

On the Microsoft sign-in screen, I clicked **Domain join instead** in the bottom-left corner to skip Microsoft account integration.

![Win10_domainjoin](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-domainjoin.png)

For the local account, I entered a username such as `local.admin` and clicked **Next**.

![Win10_localadmin](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-localadmin.png)

In the privacy settings section, I disabled all options to minimize telemetry and selected **Accept**. 

![Win10_privacy](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-privacy.png)

### 🏷️ Rename the Client 

To maintain a clean and consistent naming convention in the lab, I renamed the virtual machine immediately after setup.

From the Windows 10 desktop, I pressed `Win + X` and selected **System**. Then I clicked **Rename this PC**, entered the new hostname as `WIN10-CLIENT01`, and confirmed the prompt. 

After a system reboot, the updated name took effect and the machine was ready to be joined to the domain.

![Win10_rename](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-rename-host.png)

## 3️⃣ Domain Join Process

Once the system was renamed and rebooted, I logged in using the local account that was created during the initial Windows 10 setup.

Instead of using Settings, I used the legacy **System Properties** method to join the Windows 10 VM to the `adlab` domain.

To begin the domain join, I opened the **System** window, click **Change settings**, then hit **Change...** under **Computer Name**. In the domain field, I entered `adlab.local` and when prompted, I authenticated with the domain admin credentials.

![Win10_domain_join](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-domain-join.png)

Once confirmed, the machine processed the join request and prompted for a restart. 

After rebooting, I was presented with the option to log in as **Other user**. I signed in using the one of the non-admin domain credentials created earlier.

![Win10_domain_join](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-domain-signin.png)

After logging in with domain credentials, I opened **Command Prompt** to validate domain join and network settings.

I ran the following commands:

```cmd
whoami
ipconfig /all
```

![Win10_finalcheck](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-finalcheck.png)

> 🚩 The process documented for `WIN10-CLIENT01` was later repeated for `WIN11-CLIENT02`, with all steps mirrored, from OS installation and local setup to domain join and post-login validation.

## 4️⃣ Fix: Enable Enhanced Session Mode capabilities via GPO

After joining the Windows clients to the domain, I encountered an issue where Enhanced Session Mode stopped working in Hyper-V. Instead of loading into the desktop, the client VMs displayed an RDP-related error at login:

> "To sign in remotely, you need the right to sign in through Remote Desktop Services. By default, members of the Remote Desktop Users group have this right..."

This indicated the session was being treated as a Remote Desktop login, and the domain user lacked the necessary privileges to sign in.

![Win10_error](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-login-error.png)

### 🧪 Symptoms Observed

- Cannot initiate Enhanced Session Mode from Hyper-V Manager  
- Clipboard, drive redirection, and display scaling unavailable  
- Attempting RDP-style session results in: **"The requested session access is denied."**  
- Clients were reachable, but remote login was silently blocked by policy

### 🕵️ Root Cause

Domain Group Policy had overridden local Remote Desktop permissions. The new domain users were not members of the **Remote Desktop Users** group, and the “Allow log on through Remote Desktop Services” policy had not been updated.

- Initially granted **Remote Desktop Users** group logon rights via GPO  
- Verified that `ADLAB\Domain Users` were members of the domain-level **Remote Desktop Users**  
- **Assumed** this would reflect in the local group on each client — it didn’t  
- Local `Remote Desktop Users` group remained empty

> 🔍 GPOs can assign permissions to domain groups, but local groups must be explicitly populated via **Group Policy Preferences (GPP)**.

### 🛠️ Resolution via Group Policy

To restore Enhanced Session Mode functionality, I created a dedicated GPO with two components:

1. **Grants RDP logon rights** directly to `ADLAB\Domain Users`  
2. *(Optional)* Populates the local `Remote Desktop Users` group using GPP

### ✅ Method 1: Direct Assignment (Best for Labs)

1. On `DC01`, launch **Group Policy Management**
2. Right-click the target OU (e.g., `LabComputers`) → **Create a GPO in this domain, and Link it here…**
3. Name the policy: `Enhanced Session Fix for Endpoints`
4. Right-click the new GPO → **Edit**
5. Navigate to: `Computer Configuration` → `Windows Settings` → `Security Settings` → `Local Policies` → `User Rights Assignment`
6. Double-click: **Allow log on through Remote Desktop Services**
7. Click **Add User or Group** → enter: `Domain Users`

![Win10_gpo_edit](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-fix-gpo.png)

> 📌 This grants RDP logon rights directly, bypassing group nesting issues.

### 🔄 Method 2: Enterprise Approach via Group Policy Preferences (Optional)

This method keeps logon permissions tied to the **local** `Remote Desktop Users` group (as in enterprise setups), and uses GPP to control **who is a member** of that group.

> 📢 **Important:** If using this method, go back to the previous step and configure **Allow log on through Remote Desktop Services** to grant access to **Remote Desktop Users (local)** — _not_ directly to `ADLAB\Domain Users`.

1. In the same GPO, navigate to: `Computer Configuration → Preferences → Control Panel Settings → Local Users and Groups`
2. Right-click → **New** → **Local Group**
3. Configure the following:

   - **Action:** Update  
   - **Group name:** Remote Desktop Users  
   - **Description:** Add domain users for RDP  
   - **Members:** `ADLAB\Domain Users`

![Win10_gpp](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-fix-gpp.png)

> 💡 This method mimics enterprise behavior, RDP permission stays tied to the local group, while GPP handles group membership.

### 📋 Configure Session Redirection Policies

1. In the same GPO, go to:  
   `Computer Configuration` → `Administrative Templates` → `Windows Components` → `Remote Desktop Services` → `Remote Desktop Session Host` → `Device and Resource Redirection`
2. Enable the following settings:
   - **Do not allow clipboard redirection** → `Disabled`
   - **Do not allow drive redirection** → `Disabled`
   
![Win10_gpo_edit](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-fix-gpo-final.png)

### 📚 Best Practice Breakdown

| Approach                     | Pros                             | Cons                | Best Use                      |
|------------------------------|----------------------------------|---------------------|-------------------------------|
| Direct to Domain Users       | Simple, fast, works immediately  | Less modular        | Homelabs, small AD setups     |
| GPP + Local Group assignment | Scalable, enterprise-aligned     | Slightly more complex | Enterprise labs or prod envs |

### 🧠 Lesson Learned

- Don’t assume domain groups auto-populate local ones  
- Always test user login workflows after domain joins  
- GPO + GPP gives tight control, only if scope and evaluation are understood  

### 🔃 Apply & Test

1. Force a Group Policy update on the clients:
   ```cmd
   gpupdate /force
   ```
2. Check if Enhanced Session Fix GPO is being applied:
   ```cmd
   gpresult /scope:computer /h c:\gpo_report.html
   start c:\gpo_report.html
   ```

![Win10_gpresult](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-fix-gpresult.png)

> ✅ With this fix applied, Enhanced Session Mode now works across all clients. Domain users can RDP into lab machines, and Hyper-V usability is fully restored.

## 5️⃣ Deploy Baseline GPOs: Password Policy & Audit Logging

With domain join complete, I moved forward with applying baseline security controls via Group Policy. These initial GPOs enforce strong password practices and enable auditing of critical events, ensuring the domain is secure and log-ready for Splunk integration.

### 📌 GPO 1: Password & Lockout Policy

This policy requires linking the Default Domain Policy at the domain level (if not already linked), and then editing it directly to enforce domain-wide password and lockout settings.

**GPO Name:** `Default Domain Policy`  
**Path:** `Computer Configuration` → `Policies` → `Windows Settings` → `Security Settings` → `Account Policies`

**Settings Applied:**

### 🔐 Password Policy

- Minimum password length: `12 characters`
- Password must meet complexity requirements: `Enabled`
- Maximum password age: `30 days`
- Minimum password age: `1 day`
- Enforce password history: `24 passwords remembered`

![Win10_pwd_policy](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-pwd-policy.png)

### 🚫 Account Lockout Policy
  
- Account lockout threshold: `5 failed attempts`
- Lockout duration: `15 minutes`
- Reset account lockout counter after: `15 minutes`

![Win10_pwd_lockout](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-pwd-lockout.png)

> 🛡️ These settings apply to all domain users by default and establish a secure baseline.

### 📌 GPO 2: Advanced Audit Policy Configuration

**GPO Name:** `Audit Logging Baseline`  
**Path:** `Computer Configuration` → `Policies` → `Windows Settings` → `Security Settings` → `Advanced Audit Policy Configuration`

**Audit Categories Enabled:**

- **Logon/Logoff:** Logon, Logoff, Account Lockout  
- **Account Logon:** Credential Validation  
- **Object Access:** File Share, File System  
- **Policy Change:** Audit Policy Change  
- **Privilege Use:** Sensitive Privilege Use  
- **System:** System Integrity

![Win10_audit_logs](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-audit-logs.png)

> 📊 These logs are critical for tracking authentication activity, policy enforcement, and privileged actions. They’ll be forwarded to Splunk in Day 05.

### 🧪 Policy Validation Testing

Logged in with domain test users to trigger:

- Password complexity prompts  
- Lockout behavior  
- Authentication events in **Security logs**

![Win10_events](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-audit-events.png)

> 🔍 Validated event generation in **Event Viewer → Windows Logs → Security**

## 6️⃣ Harden Endpoint Behavior: PowerShell Logging & Local Admin Restriction

To strengthen detection capabilities and enforce least-privilege principles, I applied two additional GPOs: one to increase PowerShell visibility and one to tightly control local administrator access on lab endpoints.

### 📌 GPO 3: PowerShell Logging

**GPO Name:** `PowerShell Monitoring Baseline`  
**Path:** `Computer Configuration` → `Policies` → `Administrative Templates` → `Windows Components` → `Windows PowerShell`

**Settings Enabled:**
- **Turn on PowerShell Script Block Logging:** Enabled  
- **Turn on Module Logging:** Enabled  
- **Turn on PowerShell Transcription:** Enabled  
  - Transcript Output Directory: `C:\Transcripts`
 
![Win10_powershell_logging](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-powershell.png)

> 🧠 These settings log every executed PowerShell block, module activity, and full console input/output to both **Event Viewer** and **plain-text files**, making it easier to detect post-exploitation and tool misuse.

### 🔎 PowerShell Logging Verification

Verified:
- PowerShell transcripts saved to: `C:\Transcripts`  
- Script Block events under: `Event Viewer → Applications and Services Logs → Microsoft → Windows → PowerShell → Operational`

![Win10_pwr_ops](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-powershell-operational.png)

> 🧠 These logs are crucial for **incident detection, lateral movement analysis**, and **script-based attack visibility**, forming the telemetry backbone ahead of Splunk integration.

| Event ID | Description                             | Purpose                                   |
|----------|-----------------------------------------|-------------------------------------------|
| **4103** | PowerShell Module Logging               | Captures loaded modules and cmdlets       |
| **4104** | Script Block Logging                    | Logs full content of executed scripts     |
| **4105** | Provider “Start” Event                  | Shows when a PowerShell provider starts   |
| **4106** | Provider “Stop” Event                   | Indicates when a provider is stopped      |

### 📌 GPO 4: Disable Local Administrator Account

**GPO Name:** `Disable Local Administrator`  
**Path:** `Computer Configuration` → `Policies` → `Windows Settings` → `Security Settings` → `Local Policies` → `Security Options`

**Configuration:** Accounts: Administrator account status → **Disabled**

> 🚫 This disables the built-in local Administrator account on all targeted machines, reducing lateral movement risk. All administrative access should be handled through domain-based groups like `ADLAB\LabAdmins_group`.

### 📌 GPO 5: Local Admin Group Enforcement

**GPO Name:** `Local Admin Restriction`  
**Path:** `Computer Configuration` → `Policies` → `Windows Settings` → `Security Settings` → `Restricted Groups`

**Configuration:**
- Group Name: `Administrators`  
- Members:
  - `ADLAB\Administrator`
  - `ADLAB\LabAdmins_group`
 
![Win10_local_admins_policy](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-local-admins-policy.png)

> 🚫 This removes any unintended users or groups (e.g. `Domain Users`) from the local Administrators group on all client machines, a critical step for enforcing **least privilege** and preventing lateral movement.

### 🧩 Local Administrator Group Verification

Confirmed local administrator group membership on each client:

```cmd
net localgroup Administrators
```

![Win10_pwr_admins](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-powershell-admins.png)

> 🔒 Disabling the Administrator account prevents usage, but does not remove its group presence. This is normal and aligns with secure baseline behavior. Administrative access should instead flow through controlled domain groups.

## ✅ Day 04 Recap 

- 🧩 Created two client VMs: `WIN10-CLIENT01` and `WIN11-CLIENT01`
- 🖥️ Installed Windows 10 and 11 using ISOs from Microsoft’s Evaluation Center
- 🔐 Joined both machines to the domain via classic **System Properties** interface
- 🆔 Logged in using standard domain users (`alice.smith`, `bob.johnston`)
- 📡 Validated successful domain join using `whoami` and `ipconfig /all`
- 🔧 Resolved Enhanced Session Mode issues via custom GPO  
  - Assigned `ADLAB\Domain Users` logon rights  
  - Configured device redirection policies  
- 🛡️ Implemented security and auditing GPOs  
  - Enforced password policies and account lockouts  
  - Enabled PowerShell logging and transcript storage  
  - Verified event generation in **Event Viewer**  
- 👥 Confirmed local admin group memberships via `net localgroup Administrators`

## 🔜 Next Step

On [Day 05](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/Day05-Splunk-Logging-and-Monitoring/README.md), I’ll configure Splunk and begin sending Windows logs from the DC and clients. This will kick off the **detection engineering** and centralized monitoring phase of the HomeLab.


