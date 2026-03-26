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

## High-Level Deployment and Configuration Steps

* **Step 1:** Deploy Azure Infrastructure (Domain Controller & Client VM)
* **Step 2:** Install Active Directory Domain Services & Configure DNS
* **Step 3:** Bulk User Provisioning via PowerShell (10,000+ Users)
* **Step 4:** Implement RBAC via Security Groups & NTFS Permissions

---

## Deployment and Configuration Steps

### 1. Infrastructure Setup
Configured a Virtual Network (VNet) in Azure and deployed two Virtual Machines: a Windows Server 2022 instance acting as the Domain Controller and a Windows 10 instance for the client workstation. Verified internal connectivity and DNS resolution.

### 2. Bulk User Automation
Using **PowerShell**, I developed a script to parse employee data and automate the creation of over 10,000 unique user objects within Active Directory. This ensured consistent account attributes and password policies.

![AD Users and Computers](image_23e31b.png)

### 3. Implementing RBAC & Least Privilege
To secure departmental data, I established a security structure by creating an **"ACCOUNTANTS" Security Group**. I then configured a network share with specific **NTFS and Share permissions** to ensure only authorized users had access.

![RBAC Success](image_245e84.jpg)

### 4. Security Validation
Conducted end-to-end testing from the domain-joined client workstation. I verified that a user could only access the restricted "accounting" folder after being explicitly added to the appropriate security group, effectively demonstrating the Principle of Least Privilege.

![Workstation Login](image_2457fc.jpg)
