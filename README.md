<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

![Github Banner 4 - Active Directory Deployment and Configuration](https://github.com/user-attachments/assets/a5f19cc2-14ec-44ee-8c33-63ef4261529e)

<img src="https://github.com/AnderMendoza/AnderMendoza/raw/main/assets/line-neon.gif" width="100%">

# Setting Up User Accounts and Group Policy in Active Directory on Azure VMs

This guide walks you through the process of establishing a user database and configuring Group Policy Objects (GPOs) for Active Directory within Azure Virtual Machines.

## 🚨 Prerequisites

- [Implementing Active Directory on Azure Virtual Machines](https://github.com/mxwllslvr/implementing-active-directory-in-microsoft-azure-virtual-machines)

## Technologies Utilized

- Microsoft Azure (Compute/Virtual Machines)
- Remote Desktop Protocol (RDP) via mstsc
- Active Directory Domain Services
- PowerShell

## Operating Systems

- Windows 11 Home (24H2)
- Windows Server 2022
- Windows 10 (21H2)

## Procedure Overview

1. Building the User Database
2. Configuring Group Policy Objects

---

## Step 1: Building the User Database

![DC-1 Connection](https://github.com/user-attachments/assets/48bd2d15-04df-4330-88bc-c38034ce5276)

1. Access DC-1: Connect to the DC-1 virtual machine using mstsc with the jane_admin account (mydomain.com\jane_admin and the corresponding password).
2. Launch PowerShell ISE: From the Start menu, search for "PowerShell ISE," right-click, and select "Run as Administrator."

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 3](https://github.com/user-attachments/assets/1da0b787-1dda-4a1b-a886-f415cea4ac85)

3. Obtain the Script: Navigate to this PowerShell script. Click the "Raw" button, copy the script contents, and return to PowerShell ISE.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 4](https://github.com/user-attachments/assets/ad369a8f-b64d-417c-8af8-ef5c71c347aa)

4. Run the Script:

In PowerShell ISE, click the "New File" icon to open a blank script.

Paste the copied script into the new file.

Click the "Run" button (green play icon) to execute the script.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 5](https://github.com/user-attachments/assets/7696f2bd-6aec-42ea-a229-798ef14804fd)

5. Note the Password: While the script generates thousands of users, observe the password specified in the second line of the script. This password applies to all new users and will be needed for login tests.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 6](https://github.com/user-attachments/assets/82a8f4dc-ac01-4641-bacb-b49273fee3ef)

6. Check User Creation:
Allow the script to continue running in the background.
Open the Start menu, search for "Active Directory Users and Computers," and launch it.
Navigate to the _EMPLOYEES folder under mydomain.com to view the newly created users.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 7](https://github.com/user-attachments/assets/5bc02e3d-d629-47a0-bfbb-705bce6bc184)

7. Test User Login:
Pick a username from the _EMPLOYEES folder.
On the Client-1 VM, use mstsc to connect and attempt to log in with the selected user’s credentials (e.g., "mydomain.com\bad.teb" and the password "Password1").
Verify successful login. If it works, you’re on the right track!

## Step 2: Configuring Group Policy Objects

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 8](https://github.com/user-attachments/assets/4922c7ce-c099-4a2d-866c-317624e9a8e2)

1. Access Group Policy Management:
Log in to DC-1 using mstsc as jane_admin (mydomain.com\jane_admin).
Right-click the Start menu, select "Run," type gpmc.msc, and press Enter to open the Group Policy Management Console.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 9](https://github.com/user-attachments/assets/8f2596a0-466b-4746-80c3-5726aa1e3b4e)

2. Modify Default Domain Policy:
In the Group Policy Management Console, locate mydomain.com.
Right-click "Default Domain Policy" and choose "Edit" to open the Group Policy Management Editor.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 10](https://github.com/user-attachments/assets/b1157ac5-89f5-43a9-90f7-1b07821c3ac3)

3. Set Account Lockout Policy:
In the Editor, navigate to: Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy.

4. Define Lockout Duration:
Double-click "Account lockout duration."
Check "Define this policy setting," set the duration to 30 minutes, and click "Apply" then "OK."
A dialog may appear suggesting values for related settings (e.g., lockout threshold). Click "OK" to accept these changes.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 11](https://github.com/user-attachments/assets/9f88d47c-4962-476f-9bd9-3ffc8fe8b59a)

5. Review Policy:
Check the Account Lockout Policy settings to confirm the updated values.
Note that GPO changes may take up to 90 minutes to apply unless forced.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 12](https://github.com/user-attachments/assets/b86215ab-8ceb-4c84-9f08-a79f24c822b4)

6. Force Policy Update:
Log in to Client-1 using mstsc as jane_admin (mydomain.com\jane_admin).
Open Command Prompt and run gpupdate /force to apply the policy immediately.
Log out of Client-1 after the update completes.

![Setting Up User Accounts and Group Policy in Active Directory on Azure VMs 13](https://github.com/user-attachments/assets/e7045485-7c65-4fea-aead-6268d3640302)

7. Test Account Lockout:
On Client-1, use mstsc to connect and attempt to log in as a domain user (e.g., jus.pob) with an incorrect password six times.
The account should lock out, preventing further login attempts.

