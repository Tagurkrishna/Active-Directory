## 🛡️ PowerShell Scripts for AD Security & Automation
### 🔍 1. PowerShell Script: Assign AD Users to Security Groups
This script automatically assigns users to security groups based on their department.

📌 Script Path: Scripts/assign_user_roles.ps1
```
# Assign AD users to specific groups based on department
Import-Module ActiveDirectory

# Define role mappings
$RoleMappings = @{
    "IT"         = "IT_Admins"
    "HR"         = "HR_Managers"
    "Finance"    = "Finance_Team"
    "Security"   = "Security_Analysts"
}

# Get all users from Active Directory
$Users = Get-ADUser -Filter * -Property DisplayName, Department

foreach ($User in $Users) {
    $Dept = $User.Department
    if ($RoleMappings.ContainsKey($Dept)) {
        $GroupName = $RoleMappings[$Dept]
        
        # Add user to the appropriate AD group
        Add-ADGroupMember -Identity $GroupName -Members $User.SamAccountName
        Write-Host "Added $($User.DisplayName) to $GroupName"
    }
}
```
✅ Automatically adds users to correct security groups!


### 📊 2. PowerShell Script: Monitor AD Security Logs
This script monitors AD security logs for suspicious activities like failed logins, account lockouts, and privilege escalation attempts.

📌 Script Path: Scripts/monitor_ad_security_logs.ps1

```
# Monitor AD for security events
$LogName = "Security"
$Events = Get-WinEvent -LogName $LogName | Where-Object {
    $_.Id -in (4625, 4740, 4769, 4776, 4672)
}

foreach ($Event in $Events) {
    Write-Host "[$($Event.TimeCreated)] Event ID: $($Event.Id) - $($Event.Message)"
}
```
✅ Detects key security threats like:
🔹 Failed logins (ID 4625) → Possible brute-force attack
🔹 Account lockouts (ID 4740) → Multiple failed login attempts
🔹 Kerberos attacks (ID 4769) → Kerberoasting attempt
🔹 NTLM authentication (ID 4776) → Legacy authentication used
🔹 Privileged access (ID 4672) → Someone got Admin rights

🚀 Using this to monitor for suspicious activity in your AD environment!

### 🔐 3. Active Directory Security Hardening

✅ Apply these settings to improve AD security:

| 🔍 Issue                  | ⚠️ Impact                                    | 🛠️ Fix                              |
|---------------------------|----------------------------------------------|--------------------------------------|
| **Weak Password Policies** | Increases risk of brute-force attacks       | Enforce complex passwords & MFA     |
| **Unused Admin Accounts**  | Attackers exploit old admin accounts        | Disable inactive admin accounts     |
| **Excessive Group Membership** | Lateral movement risk                  | Regularly audit & remove unnecessary users |
| **Default Service Accounts** | Attackers extract service account hashes  | Use Managed Service Accounts (MSAs) |


### 📌 Hardening Configurations
🔸 Disable Weak NTLM Authentication

powershell

Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Lsa" -Name "LmCompatibilityLevel" -Value 5

🔸 Enforce LDAP Signing

powershell

Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\NTDS\Parameters" -Name "LDAPServerIntegrity" -Value 2

🔸 Enable Event Logging for Threat Detection

powershell:

auditpol /set /subcategory:"Logon" /success:enable /failure:enable

### 🚀 With these security settings, your AD environment is more resistant to attacks!

## 📌 Conclusion

This project demonstrates Active Directory setup, automation, security hardening, and monitoring using PowerShell scripts. The goal is to improve security, automate user management, and detect potential threats.
