<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

![Github Banner 4 - Active Directory Deployment and Configuration](https://github.com/user-attachments/assets/a5f19cc2-14ec-44ee-8c33-63ef4261529e)

<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

# Setting Up User Accounts and Group Policy in Active Directory on Azure VMs

This guide walks you through establishing a user database and configuring Group Policy Objects (GPOs) for Active Directory within Azure Virtual Machines.

## 🚨 Prerequisites

- [Implementing Active Directory on Azure Virtual Machines](https://github.com/mxwllslvr/implementing-active-directory-in-microsoft-azure-virtual-machines)

## Technologies Utilized

- Microsoft Azure (Compute/Virtual Machines)
- Remote Desktop Protocol (RDP) via mstsc
- Active Directory Domain Services
- PowerShell ISE
- Group Policy Management Console

## Operating Systems

- Windows 11 Home (24H2)
- Windows Server 2022
- Windows 10 (21H2)

## Procedure Overview

1. Building the User Database
2. Configuring Group Policy Objects
3. Testing Account Lockout Policies
4. Managing User Accounts

---

## Step 1: Building the User Database

### Connect to Domain Controller

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 1](https://github.com/user-attachments/assets/fd1b21e1-b79c-42cc-b726-4d8fff25cbe9)

1. **Access DC-1**: Connect to the DC-1 virtual machine using mstsc with the jane_admin account (`mydomain.com\jane_admin`) and the corresponding password
2. **Launch PowerShell ISE**: From the Start menu, search for "PowerShell ISE," right-click, and select "Run as Administrator"

### Obtain and Execute User Creation Script

![PowerShell Script Setup](https://github.com/user-attachments/assets/1da0b787-1dda-4a1b-a886-f415cea4ac85)

1. **Obtain the Script**: Navigate to the PowerShell script repository. Click the "Raw" button, copy the script contents, and return to PowerShell ISE
2. **Prepare Script Environment**: In PowerShell ISE, click the "New File" icon to open a blank script editor

![Script Execution](https://github.com/user-attachments/assets/ad369a8f-b64d-417c-8af8-ef5c71c347aa)

3. **Execute User Creation**:
   - Paste the copied script into the new file
   - Click the "Run" button (green play icon) to execute the script
   - The script will generate thousands of user accounts automatically

### Monitor User Creation Process

![Password Configuration](https://github.com/user-attachments/assets/7696f2bd-6aec-42ea-a229-798ef14804fd)

1. **Note Default Password**: Observe the password specified in the second line of the script (typically "Password1"). This password applies to all newly created users and will be needed for login testing
2. **Allow Background Processing**: Let the script continue running while proceeding to verification steps

### Verify User Account Creation

![Active Directory Verification](https://github.com/user-attachments/assets/82a8f4dc-ac01-4641-bacb-b49273fee3ef)

1. **Open Active Directory Console**: Launch "Active Directory Users and Computers" from the Start menu
2. **Navigate to User Container**: Expand mydomain.com and locate the _EMPLOYEES organizational unit to view the newly created user accounts
3. **Review Generated Users**: Browse through the user list to confirm successful account creation

### Test User Authentication

![User Login Test](https://github.com/user-attachments/assets/5bc02e3d-d629-47a0-bfbb-705bce6bc184)

1. **Select Test User**: Choose a username from the _EMPLOYEES folder for authentication testing
2. **Connect to Client VM**: On Client-1, use mstsc to establish a connection
3. **Authenticate User**: Attempt login with selected credentials (format: `mydomain.com\username` with "Password1")
4. **Verify Access**: Confirm successful domain authentication and desktop access

**Observation**: The PowerShell script successfully created a comprehensive user database, with all accounts configured for domain authentication using standardized credentials.

---

## Step 2: Configuring Group Policy Objects

### Access Group Policy Management

![Group Policy Console](https://github.com/user-attachments/assets/4922c7ce-c099-4a2d-866c-317624e9a8e2)

1. **Connect to Domain Controller**: Log into DC-1 using mstsc as jane_admin (`mydomain.com\jane_admin`)
2. **Launch GPO Console**: Right-click the Start menu, select "Run," type `gpmc.msc`, and press Enter to open the Group Policy Management Console

### Configure Default Domain Policy

![Policy Editor Access](https://github.com/user-attachments/assets/8f2596a0-466b-4746-80c3-5726aa1e3b4e)

1. **Locate Domain Policy**: In the Group Policy Management Console, navigate to mydomain.com
2. **Edit Policy**: Right-click "Default Domain Policy" and select "Edit" to open the Group Policy Management Editor

### Implement Account Lockout Policy

![Account Lockout Configuration](https://github.com/user-attachments/assets/b1157ac5-89f5-43a9-90f7-1b07821c3ac3)

1. **Navigate to Security Settings**: Follow the path: Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy
2. **Configure Lockout Duration**:
   - Double-click "Account lockout duration"
   - Check "Define this policy setting"
   - Set duration to 30 minutes
   - Click "Apply" then "OK"
3. **Accept Related Settings**: When prompted, click "OK" to accept suggested values for lockout threshold and other related policies

### Review and Validate Policy Settings

![Policy Verification](https://github.com/user-attachments/assets/9f88d47c-4962-476f-9bd9-3ffc8fe8b59a)

1. **Confirm Configuration**: Review the Account Lockout Policy settings to verify updated values
2. **Note Propagation Time**: GPO changes typically require up to 90 minutes for natural propagation unless manually forced

**Observation**: Group Policy Objects provide centralized security management, enabling consistent account lockout policies across all domain-joined systems.

---

## Step 3: Testing Account Lockout Policies

### Force Policy Application

![Policy Update](https://github.com/user-attachments/assets/b86215ab-8ceb-4c84-9f08-a79f24c822b4)

1. **Connect to Client System**: Log into Client-1 using mstsc as jane_admin (`mydomain.com\jane_admin`)
2. **Apply Policy Immediately**: Open Command Prompt and execute `gpupdate /force` to apply the policy without waiting for natural propagation
3. **Complete Update Process**: Allow the command to finish, then log out of Client-1

### Execute Lockout Test

![Account Lockout Test](https://github.com/user-attachments/assets/e7045485-7c65-4fea-aead-6268d3640302)

1. **Initiate Lockout Scenario**: On Client-1, use mstsc to connect and attempt login as a domain user (e.g., jus.pob)
2. **Generate Failed Attempts**: Enter incorrect passwords repeatedly (six failed attempts should trigger the lockout)
3. **Confirm Lockout Status**: Verify that the account becomes locked and prevents further authentication attempts

### Unlock Locked Account

![Account Unlock Process](https://github.com/user-attachments/assets/6a27a817-c04b-42a6-8be2-fe5996e031db)

1. **Access Domain Controller**: Return to DC-1 and log in as jane_admin
2. **Locate Locked User**: In "Active Directory Users and Computers," right-click mydomain.com, select "Find," enter the username, and click "Find Now"
3. **Unlock Account**:
   - Open the user's Properties dialog
   - Navigate to the "Account" tab
   - Check "Unlock account" checkbox
   - Click "Apply" then "OK"

### Verify Account Recovery

![Login Verification](https://github.com/user-attachments/assets/eed2791c-7532-4d4e-ad3d-3a02941a0fb3)

1. **Test Restored Access**: Use mstsc to log into Client-1 as the previously locked user (`mydomain.com\jus.pob` with "Password1")
2. **Confirm Authentication**: Verify successful login by running `whoami` in PowerShell or Command Prompt to display current user context

**Observation**: The account lockout policy successfully protected against brute force attacks, while administrative controls allowed for efficient account recovery procedures.

---

## Step 4: Managing User Accounts

### Account Disable Testing

![Account Disable](https://github.com/user-attachments/assets/1dbe2db4-9227-45ac-9bfc-14848f162435)

1. **Locate Target User**: In "Active Directory Users and Computers," find a user account via the _EMPLOYEES folder or search function
2. **Disable Account**: Right-click the selected user and choose "Disable Account," then confirm the action
3. **Test Disabled Status**: Attempt login as the disabled user on Client-1 to verify account inaccessibility

### Password Reset Procedures

![Password Reset](https://github.com/user-attachments/assets/6213f24e-2f9d-4e9c-8fc4-a704fc9a00ab)

1. **Access User Properties**: Locate the target user using the "Find" feature or by browsing the _EMPLOYEES organizational unit
2. **Reset Credentials**:
   - Right-click the user and select "Reset Password"
   - Enter new password and confirmation
   - Apply changes
3. **Validate New Credentials**: Test the updated password by logging into Client-1 using mstsc

### Security Event Monitoring

![Event Log Analysis](https://github.com/user-attachments/assets/6213f24e-2f9d-4e9c-8fc4-a704fc9a00ab)

1. **Access Event Viewer**: On Client-1, log in as jane_admin and launch "eventvwr.msc" as Administrator
2. **Navigate Security Logs**: Expand "Windows Logs," right-click "Security," and select "Find"
3. **Search Authentication Events**: Enter the username (e.g., jus.pob) to locate login attempt records with detailed timestamps and event codes
4. **Analyze Security Data**: Review failed authentication events to understand attack patterns and security incidents

**Observation**: Comprehensive user account management includes proactive monitoring through event logs, enabling administrators to track authentication patterns and respond to security incidents effectively.

<br/><br/>

<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

<br/>

<div align="center"> <img src="https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExeXp4bXJvNDc4NGRvd3dyYzYxYnBra3lncHV1dmQwb2g5d3I3ajRmdyZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3o6ozomjwcQJpdz5p6/giphy.gif" width="75%"> </div>

<br/>

<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

## Conclusion

This procedure successfully demonstrated comprehensive Active Directory user management and Group Policy configuration within Azure Virtual Machines. Through PowerShell automation, we created a substantial user database, implemented security policies, and tested account lockout mechanisms to validate organizational security controls.

**Key Learning Outcomes:**
- Automated user account creation using PowerShell scripting
- Group Policy Object configuration and deployment
- Account lockout policy implementation and testing
- User account management including disable/enable and password reset procedures
- Security event monitoring and analysis techniques

This exercise provides fundamental skills for enterprise Active Directory administration, demonstrating both automated provisioning and manual management techniques essential for maintaining secure domain environments. To optimize costs, ensure both VMs are stopped in Azure when not actively in use.

---

<div align="center">

<a href="HTTP://www.github.com/mxwllslvr">- BACK TO MAIN PAGE -</a>

</div>
