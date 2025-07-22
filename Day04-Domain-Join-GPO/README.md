## 9️⃣ Windows Clients Setup & Domain Join

With the domain controller, DNS, and DHCP services now fully configured, I moved on to provisioning two Windows client machines, one **Windows 10** and one **Windows 11** — and joining them to the `adlab.local` domain.

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

### 💿 Install Windows on the VMs

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

### 🧑‍💼 Domain Join Process

Once the system was renamed and rebooted, I logged in using the local account that was created during the initial Windows 10 setup.

Instead of using Settings, I used the legacy **System Properties** method to join the Windows 10 VM to the `adlab` domain.

To begin the domain join, I opened the **System** window, click **Change settings**, then hit **Change...** under **Computer Name**. In the domain field, I entered `adlab.local` and when prompted, I authenticated with the domain admin credentials.

![Win10_domain_join](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-domain-join.png)

Once confirmed, the machine processed the join request and prompted for a restart. 

After rebooting, I was presented with the option to log in as **Other user**. I signed in using the one of the non-admin domain credentials created earlier.

![Win10_domain_join](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-domain-signin.png)

After logging in with domain credentials, I opened **Command Prompt** to validate domain join and network settings.

I ran the following commands:

```powershell
whoami
ipconfig /all
```

![Win10_finalcheck](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/images/AD-VM/WinSrv-win10-finalcheck.png)

> 🚩 The process documented for `WIN10-CLIENT01` was later repeated for `WIN11-CLIENT02`, with all steps mirrored — from OS installation and local setup to domain join and post-login validation.

## 🔁 Day 03 Recap — Active Directory Foundation & Domain Join

This was a pivotal day in the lab’s architecture. I spined up the full Active Directory backbone, integrated critical network services, and validated identity management by joining client endpoints to the domain. 

### 🧱 Core Accomplishments

- ✅ Deployed a Windows Server 2019 VM (Gen 2) to act as the **Domain Controller**
- ✅ Installed and configured **Active Directory Domain Services (AD DS)**
- ✅ Promoted the server to **`adlab.local`** domain controller with DNS and DHCP roles
- ✅ Assigned a static IP to the DC and configured **DNS forwarders** to pfSense
- ✅ Deployed and configured **DHCP scope** for the AD subnet (`10.0.3.100–200`)
- ✅ Installed and configured **Active Directory Certificate Services** (Enterprise Root CA)

### 🖥️ Client Integration

- 🧩 Created two client VMs: `WIN10-CLIENT01` and `WIN11-CLIENT01`
- 🖥️ Installed Windows 10 and 11 using ISOs from Microsoft’s Evaluation Center
- 🔐 Joined both machines to the domain via classic **System Properties** interface
- 🆔 Logged in using standard domain users (`alice.smith`, `bob.johnston`)
- 📡 Validated successful domain join using `whoami` and `ipconfig /all`

## 🔜 Next Step

On [Day 04](https://github.com/gkopacz/CyberSec-HomeLab/blob/main/Day04-Splunk-Logging-and-Monitoring), I’ll configure Splunk and begin sending Windows logs from the DC and clients. This will kick off the **detection engineering** and centralized monitoring phase of the HomeLab.


