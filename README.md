# 🖥️ Active Directory Home Lab Setup

> **A personal project where I built an Active Directory (AD) lab from scratch, configuring Windows Server, managing users, setting up GPOs, and securing a network environment.**

![Active Directory Lab]([https://github.com/Tagurkrishna/Active-Directory/blob/main/Active%20directory.jpg))

## 📌 Overview
In this project, I set up a **Windows Server-based Active Directory environment** in a **virtualized lab** to simulate a real-world IT infrastructure. The goal was to gain hands-on experience in domain management, Group Policy, user administration, and network security.

### 🎯 Key Objectives:
- Deploy **Active Directory Domain Services (AD DS)**
- Configure **Windows 10 client machines** and join them to the domain
- Manage **Users, Groups, and Organizational Units (OUs)**
- Implement **Group Policy Objects (GPOs)**
- Configure **DNS and DHCP**
- Automate tasks with **PowerShell scripts**

---

## 💻 System Requirements

| Component          | Minimum Requirement       | Recommended          |
|--------------------|--------------------------|----------------------|
| **Host Machine**   | Virtualization Support (Windows/Linux/macOS) | 16GB+ RAM, SSD |
| **Virtualization Software** | VMware / VirtualBox / Hyper-V | VMware Workstation |
| **RAM (Server)**  | 4GB                         | 8GB+                 |
| **RAM (Client)**  | 2GB                         | 4GB+                 |
| **Storage**       | 60GB+                        | 100GB+               |
| **OS**           | Windows Server 2019/2022, Windows 10/11 | Latest Updates |

---

## 📂 Lab Setup & Configuration

### 🚀 Setting Up Windows Server & Installing Active Directory

#### ✅ Installing Windows Server
1. Download **Windows Server 2019/2022 ISO**.
2. Create a **VM** in **VMware/VirtualBox** with:
   - **2 vCPUs**, **4GB RAM**, **60GB Storage**.
3. Install Windows Server and configure **Administrator Password**.

#### ✅ Installing Active Directory Domain Services (AD DS)
1. Open **Server Manager** → Click **Manage** → **Add Roles and Features**.
2. Select **Active Directory Domain Services (AD DS)** and install it.
3. Promote the server to a **Domain Controller**:
   - Choose **"New Forest"** and set **Domain Name** (e.g., `lab.local`).
   - Restart the server.

🔹 **Verification:**  
- Open **Active Directory Users and Computers (ADUC)**: `dsa.msc`.
- Open **DNS Manager**: `dnsmgmt.msc` → Ensure `lab.local` exists.

---

### 🔗 Adding a Windows 10 Client to the Domain

1. Create a **Windows 10 VM**:
   - **2GB RAM**, **1 vCPU**, **40GB Storage**.
   - Ensure it's on the same **Internal Network** as the Domain Controller.
   
2. **Join Windows 10 to the Domain**:
   - Open **System Properties** → Click **Change Settings**.
   - Under **Computer Name**, click **Change**.
   - Select **Domain** and enter:
     ```
     lab.local
     ```
   - Enter **Domain Admin Credentials**.
   - Restart the machine.

✅ **Verify Join:**  
Login using **Domain Credentials** (`lab\username`).

---

### 👥 User Management: Creating Users, OUs, and Groups

#### 🗂️ Creating Organizational Units (OUs)
1. Open **Active Directory Users and Computers (ADUC)** (`dsa.msc`).
2. Right-click **Your Domain (lab.local)** → **New** → **Organizational Unit**.
3. Create OUs for **IT, HR, Finance**, etc.

#### 👤 Creating Users
1. Inside an **OU**, right-click → **New** → **User**.
2. Enter **First Name, Last Name, Logon Name**.
3. Set a **Password** and select:
   
User must change password at next logon


#### 📜 Automating Bulk User Creation (PowerShell)
To create multiple users at once, I used this **PowerShell script**:
- **Securtiy Hardening**: [Security Hardening]([https://github.com/Tagurkrishna/Active-Directory/blob/main/security-hardening.md)

```powershell
Import-Module ActiveDirectory
$users = Import-Csv -Path "C:\path\to\users.csv"
foreach ($user in $users) {
 New-ADUser -Name $user.Name -GivenName $user.GivenName -Surname $user.Surname -SamAccountName $user.SamAccountName -UserPrincipalName $user.UserPrincipalName -Path "OU=Users,DC=lab,DC=local" -AccountPassword (ConvertTo-SecureString $user.Password -AsPlainText -Force) -Enabled $true
}
```

---

## 🏗️ **Active Directory Setup & Configuration**
### **🛠️ Installing & Configuring Active Directory**
1. **Install AD DS Role**  
   - Open **Server Manager** → **Add Roles & Features**  
   - Select **Active Directory Domain Services (AD DS)**  
   - Complete the wizard & promote the server to a domain controller.  

2. **Create a New Forest**  
   - Domain Name: `lab.local`  
   - Set **NetBIOS Name**: `LAB`  
   - Enable **DNS Server**  

---

## 🔐 **Group Policy Objects (GPOs) - Centralized Management**
### 🏛️ What are GPOs?
GPOs allow **centralized control** over security settings, software installations, and user configurations.

### 🛠️ Creating & Applying a GPO  
1. Open **Group Policy Management** (`gpmc.msc`).  
2. Right-click **Your Domain (`lab.local`)** → **Create a GPO in this domain, and Link it here**.  
3. Name the GPO (e.g., `Password Policy`).  
4. Right-click the GPO → **Edit** → Modify settings like:

Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy

5. **Applying a GPO on Clients:** Run the following command to force an update:
```cmd
gpupdate /force
```

### 🌐 Configuring DNS & DHCP
## 📌 DNS Setup
Open DNS Manager (dnsmgmt.msc) → Verify Forward Lookup Zones.
Create a Reverse Lookup Zone for 192.168.1.x subnet.
## 📌 DHCP Configuration
Install DHCP via Server Manager → Add Roles and Features.
Configure a DHCP Scope:
IP Range: 192.168.1.100 - 192.168.1.200
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.1.1
Activate the Scope.
### ✅ Verify DHCP Assignment
Run the following command on a client machine to check the assigned IP:
ipconfig /all

---

### 🛡️ Monitoring and Maintaining the Active Directory Environment

#### 📈 Performance Monitoring
1. **Utilize Built-in Tools**:
   - **Performance Monitor** (`perfmon.msc`): Track real-time performance metrics.
   - **Resource Monitor**: Monitor CPU, memory, disk, and network usage.

2. **Set Up Performance Baselines**:
   - Establish benchmarks for normal performance to identify anomalies.

#### 🔄 Regular Maintenance Tasks
1. **Backup Active Directory**:
   - Use **Windows Server Backup** to schedule regular backups.
   - Ensure **System State** is included to capture AD data.

2. **Monitor Replication**:
   - Use **Repadmin** tool to check replication status:
     ```cmd
     repadmin /replsummary
     ```

3. **Review Event Logs**:
   - Regularly inspect **Event Viewer** for warnings or errors related to AD.

#### 🔐 Security Practices
1. **Implement Least Privilege**:
   - Assign users the minimum rights necessary for their roles.

2. **Regular Password Policies**:
   - Enforce strong password policies and regular password changes.

3. **Audit Logs**:
   - Enable auditing to track changes and access within the AD environment.

---

## 📝 Conclusion

By following this comprehensive guide, I've established a functional Active Directory lab environment. This setup not only enhances my understanding of AD infrastructure but also provides a safe space to experiment and learn. The skills acquired through this project are foundational for managing enterprise-level IT environments.

---

## 📂 Repository Structure

```text
Active-Directory-Home-Lab/
│── README.md             # Main Documentation
│── Scripts/
│   ├── bulk_user_creation.ps1  # Creates multiple users in AD
│   ├── gpo_update.ps1          # Forces GPO update
│── Images/                     # Screenshots & Diagrams
│── Workflows/
│   ├── network_diagram.md      # Network Topology
│   ├── troubleshooting.md      # Common Issues & Fixes
```
