
# On-premises Active Directory & RBAC Deployed in the Cloud (Azure)

This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines, including automated user provisioning and Role-Based Access Control (RBAC).

## Environments and Technologies Used

* **Microsoft Azure** (Virtual Machines/Compute)
* **Remote Desktop** (RDP)
* **Active Directory Domain Services** (AD DS)
* **PowerShell** (Automation)

## Operating Systems Used

* **Windows Server 2022**
* **Windows 10**

---

## High-Level Deployment and Configuration Steps

* **Step 1:** Deploy Azure Infrastructure (Domain Controller & Client VM)
* **Step 2:** Install Active Directory Domain Services & Configure DNS
* **Step 3:** Bulk User Provisioning via PowerShell (10,000+ Users)
* **Step 4:** Implement RBAC via Security Groups & NTFS Permissions

---

## Deployment and Configuration Steps

### 1. Infrastructure Setup & DNS Configuration
I configured a Virtual Network (VNet) in Azure and deployed two Virtual Machines. A Windows Server 2022 instance was promoted to a Domain Controller (`DC-1`), and a Windows 10 instance was joined as the client workstation. I verified DNS health by confirming A-record resolution through the command line.

<img width="800" alt="Lab 6-Successful-DNS-A-Record-Verification-DC1" src="https://github.com/user-attachments/assets/ca49b6a9-22fb-4050-9764-5349e147399c" />

---

### 2. Bulk User Automation
Using **PowerShell ISE**, I developed and executed a script to parse employee data and automate the creation of over 10,000 unique user objects within the domain. This ensured consistent account attributes and enterprise-wide password policies.

<img width="800" alt="10 000-users-gen" src="https://github.com/user-attachments/assets/042d75e7-cb64-45a0-a173-54c75f79284a" />

---

### 3. Implementing RBAC & Least Privilege
To secure departmental data, I established a security structure by creating specific Security Groups. I managed user properties and group memberships (such as Domain Admins vs. Domain Users) to ensure the Principle of Least Privilege was enforced across the network.

<img width="400" alt="Permissions" src="https://github.com/user-attachments/assets/8bdbc3ad-716f-4740-8a5f-5f5df3aab2c2" />

---

### 4. Security Validation
I conducted end-to-end testing from the domain-joined client workstation. By verifying access to restricted network shares and testing login capabilities for the newly created automated users, I confirmed that the Active Directory environment was fully functional and secure.
