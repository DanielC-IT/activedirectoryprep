
# Active Directory Setup Using Microsoft Azure

<img width="640" height="400" alt="image" src="https://github.com/user-attachments/assets/58d300fc-0acf-4f4e-b0e3-aceef6b1eb66" />


## Project Overview

This project demonstrates the setup and configuration of an Active Directory lab environment using Microsoft Azure and two Windows Virtual Machines. The lab consists of deploying a Windows Server 2022 Domain Controller and a Windows 11 client machine, configuring a virtual network, assigning a static private IP address, setting up DNS, and verifying network connectivity between both machines.

The goal is to prepare the environment for installing Active Directory Domain Services (AD DS) and managing domain users and computers.

## Environment and Technologies Used

- Cloud Platform: Microsoft Azure
- Domain Controller: Windows Server 2022 Datacenter Azure Edition
- Client Machine: Windows 11 Pro
- Virtualization: Azure Virtual Machines
- Networking: Azure Virtual Network (VNet)
- DNS Configuration: Custom DNS Server
- Remote Access: Remote Desktop Connection (RDP)
- Network Troubleshooting: Windows PowerShell
- Network Configuration: Static Private IP Address

## Demonstration

### 1. Create an Azure Resource Group

A resource group is used to organize and manage related Azure resources.

1. Log in to the [Microsoft Azure Portal](https://portal.azure.com/).
2. Search for Resource Groups.
3. Select Create.
4. Enter `Active-Directory` as the resource group name.
5. Select `East US 2` as the region.
6. Select Review + Create.
7. Select Create to finish.

<img width="819" height="398" alt="1" src="https://github.com/user-attachments/assets/76fcbed9-3618-4230-b9a6-9c8bb640c068" />

### 2. Create a Virtual Network

The virtual network allows both virtual machines to communicate with each other.

1. Search for Virtual Networks in Azure.
2. Select Create.
3. Enter the following information:
   - Name: `Active-Directory-VN`
   - Resource Group: `Active-Directory`
   - Region: `East US 2`
4. Configure the default subnet:
   - Subnet Name: `default`
   - Address Range: `10.0.0.0/24`
5. Select Review + Create.
6. Select Create.

<img width="848" height="735" alt="2" src="https://github.com/user-attachments/assets/1a54c202-556b-434d-a106-5ab588297d74" />

### 3. Create the Domain Controller Virtual Machine (DC-1)

The Domain Controller will run Windows Server 2022 and eventually host Active Directory Domain Services.

1. Navigate to Virtual Machines in Azure.
2. Select Create → Azure Virtual Machine.
3. Configure the following settings:
   - Resource Group: `Active-Directory`
   - Virtual Machine Name: `DC-1`
   - Region: `East US 2`
   - Image: `Windows Server 2022 Datacenter`
   - Username: Create an administrator username.
   - Password: Create a secure password.
4. Select an appropriate VM size.
5. Accept the required licensing terms.
6. Navigate to the Networking tab.
7. Select the following:
   - Virtual Network: `Active-Directory-VN`
   - Subnet: `default (10.0.0.0/24)`
8. Select Review + Create.
9. Select Create to deploy the VM.

<img width="866" height="1111" alt="3" src="https://github.com/user-attachments/assets/d8635221-654f-4de5-8ffc-72777c177cea" />

### 4. Create the Client Virtual Machine (Client-1)

Next, we will create a Windows 11 machine that will eventually join our Active Directory domain.

1. Navigate to Virtual Machines in Azure.
2. Select Create → Azure Virtual Machine.
3. Configure the following settings:
   - Resource Group: `Active-Directory`
   - Virtual Machine Name: `Client-1`
   - Region: `East US 2`
   - Image: `Windows 11 Pro`
   - Username: Create an administrator username.
   - Password: Create a secure password.
4. Accept the required licensing terms.
5. Navigate to the Networking tab.
6. Select the following:
   - Virtual Network: `Active-Directory-VN`
   - Subnet: `default (10.0.0.0/24)`
7. Select Review + Create.
8. Select Create to deploy the VM.

<img width="857" height="1117" alt="5" src="https://github.com/user-attachments/assets/0aaf1a35-490b-482f-b216-89fbb799d3d3" />

### 5. Configure a Static Private IP Address for DC-1

A static private IP address ensures that our Domain Controller maintains a consistent network address.

1. Navigate to Virtual Machines in Azure.
2. Select `DC-1`.
3. Under Networking, select Network Settings.
4. Open the network interface associated with DC-1.
5. Select IP Configurations.
6. Select `ipconfig1`.
7. Change the IP allocation from Dynamic to Static.
8. Keep the currently assigned private IP address.
9. Select Save.
10. Copy or write down the private IP address. We will need it when configuring DNS on Client-1.

<img width="276" height="571" alt="6" src="https://github.com/user-attachments/assets/098875f5-d232-43d4-add6-b5d112f09346" />

<img width="615" height="509" alt="7" src="https://github.com/user-attachments/assets/ed22113b-2dbc-495f-af31-4525e92c1694" />

### 6. Connect to DC-1 Using Remote Desktop

We will use Remote Desktop Connection to access our Windows Server machine.

1. Navigate to Virtual Machines in Azure.
2. Select `DC-1`.
3. Copy its Public IP Address.
4. On your local computer, press `Windows + R`.
5. Type `mstsc` and press Enter.
6. Enter DC-1's public IP address.
7. Select Connect.
8. Log in using the administrator credentials created during VM setup.

<img width="405" height="245" alt="9" src="https://github.com/user-attachments/assets/38eb1516-0cf1-4f79-8ae0-fbed4528128a" />

### 7. Verify Windows Server Version

Once connected to DC-1, verify that the correct version of Windows Server is installed.

1. Right-click the Windows Start button.
2. Select System.
3. Locate Windows Specifications.
4. Verify that the edition displays:  Windows Server 2022 Datacenter Azure Edition

<img width="433" height="200" alt="10" src="https://github.com/user-attachments/assets/4bf3d7da-e773-4b1c-9c81-3592a6d6d740" />

### 8. Configure Windows Firewall on DC-1

For this lab, we need to allow Client-1 to communicate with DC-1 using ICMP (Ping).

1. On DC-1, press `Windows + R`.
2. Type `wf.msc` and press Enter.
3. When the Windows Defender Firewall window opens, click on **Windows Defender Firewall Properties** and disable the firewall on all 3 profiles: Domain, Private, and Public

In this example, we do this only for testing purposes; Windows firewalls shouldn't be turned off.

<img width="409" height="226" alt="11" src="https://github.com/user-attachments/assets/05c7481f-de65-46b2-913a-63260c1d6c6d" />

<img width="403" height="458" alt="12" src="https://github.com/user-attachments/assets/8ca502dc-7b77-4ba9-8c24-2e6c3d5daa6f" />

### 9. Configure Client-1 DNS Settings

DNS (Domain Name System) allows computers to locate network resources and domain services. We need to configure Client-1 to use DC-1 as its DNS server.

1. Return to the Azure Portal.
2. Navigate to Virtual Machines.
3. Select `Client-1`.
4. Under Networking, select Network Settings.
5. Open the network interface associated with Client-1.
6. Select DNS Servers under Settings.
7. Choose Custom.
8. Enter DC-1's private IP address in the Custom DNS Servers field.
9. Select Save or Apply.

<img width="1152" height="639" alt="13" src="https://github.com/user-attachments/assets/e3628081-6d3c-406f-8c35-f2a75949f385" />


### 10. Restart Client-1

To apply the new DNS configuration:

1. Navigate to Virtual Machines in Azure.
2. Select `Client-1`.
3. Select Restart.
4. Wait for the VM to finish restarting.

<img width="1227" height="218" alt="14" src="https://github.com/user-attachments/assets/da60de45-ec19-4728-865b-95b65e2d2d23" />

### 11. Connect to Client-1 Using Remote Desktop

Now we will connect to our Windows 11 client machine.

1. Navigate to Virtual Machines in Azure.
2. Select `Client-1`.
3. Copy its Public IP Address.
4. On your local computer, press `Windows + R`.
5. Type `mstsc` and press Enter.
6. Enter Client-1's public IP address.
7. Select Connect.
8. Log in using the administrator credentials created during VM setup.

### 12. Test Network Connectivity Between Both Virtual Machines

We will use Windows PowerShell to verify that Client-1 can communicate with DC-1.

#### Test 1: Ping DC-1

1. On Client-1, open Windows PowerShell as Administrator.
2. Enter the following command:

```powershell
ping <DC-1-Private-IP>
```

Replace `<DC-1-Private-IP>` with the actual private IP address assigned to DC-1.

Example:

```powershell
ping 10.0.0.4
```

If everything is configured correctly, you should receive replies from DC-1.

#### Test 2: Verify DNS Configuration

While still in PowerShell, enter:

```powershell
ipconfig /all
```

Locate the network adapter information and find the DNS Servers section.

You should see DC-1's private IP address listed as the DNS server.

This confirms that Client-1 is configured to use DC-1 for DNS.

<img width="1112" height="625" alt="15" src="https://github.com/user-attachments/assets/c0692642-3351-4c49-b2a6-c882ce1ffe4e" />

<img width="1112" height="625" alt="15" src="https://github.com/user-attachments/assets/9254f454-ec83-496a-b567-60b446fb6806" />


## Project Completion

Successfully deployed and configured a two-VM environment in Microsoft Azure.

The lab now includes:

- Created an Azure Resource Group.
- Deployed a Virtual Network with a default subnet.
- Created a Windows Server 2022 Domain Controller VM.
- Created a Windows 11 Pro Client VM.
- Configured a static private IP address for DC-1.
- Configured Client-1 to use DC-1 as its DNS server.
- Established and verified network connectivity between both virtual machines.



**Note:** This is a learning environment deployed in Microsoft Azure. Restrict Remote Desktop access to trusted IP addresses, use secure credentials, and shut down or remove resources when finished to avoid unnecessary Azure charges.
