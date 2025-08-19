# Hands-On Lab: Microsoft Intune Endpoint Management for Windows
**Author:** Drayton Rolle
**Date:** August 19, 2025

## 📖 Introduction
This project documents the end-to-end process of building a functional endpoint management environment using Microsoft Intune and Microsoft Entra ID. The goal was to configure a security framework for a new Windows 11 endpoint, enforce compliance, and test access control—simulating a real-world device provisioning and security scenario.

This guide is structured as a detailed tutorial so that others can replicate the lab.

### Skills Demonstrated
* **Cloud Identity & Access Management:** Microsoft Entra ID, Conditional Access Policies
* **Modern Endpoint Management:** Microsoft Intune (MDM), Device Enrollment, Configuration Profiles, Compliance Policies
* **Virtualization:** Hyper-V Setup and Virtual Machine Provisioning
* **Operating System Deployment:** Windows 11 Installation and Configuration
* **IT Troubleshooting:** Diagnosing and remediating a device compliance failure by analyzing policy status and making targeted configuration changes.

---

## 🏗️ Phase 1: Environment Setup & Initial Configuration

The first step is to create a sandboxed Microsoft 365 environment and configure Intune for device enrollment.

### 1.1 Provision a Microsoft 365 Tenant
A 30-day trial provides a new tenant with all the required features, including Intune and Microsoft Entra ID P2.

> 👉 **Action:** Navigate to the **Microsoft 365 Business Premium** trial page and complete the sign-up process. This creates a new administrator account and a sandbox domain (e.g., `yourcompany.onmicrosoft.com`).

_...insert your screenshot of the completed "You're all set to use Microsoft 365" page here..._

### 1.2 Configure Automatic Enrollment for Windows
This setting allows any Windows device joined to our Entra ID tenant to be automatically enrolled into Intune.

> 👉 **Action:**
> 1. From the Microsoft 365 Admin Center, navigate to the **Microsoft Intune admin center**.
> 2. Go to **Devices** > **Enrollment**.
> 3. Select **Automatic Enrollment**.
> 4. Set the **MDM user scope** to **All** and click **Save**.

_...insert your screenshot showing the MDM user scope set to "All" here..._

---

## 📜 Phase 2: Defining Security Baselines with Policies

With the environment ready, two critical policies were created to define the security posture for the organization.

### 2.1 Create the Windows Compliance Policy
This policy defines the rules a Windows device must follow to be considered "compliant" and trustworthy.

> 👉 **Action:**
> 1. In the Intune admin center, go to **Devices** > **Compliance**.
> 2. Click **+ Create policy**.
> 3. Select **Windows 10 and later** as the platform.
> 4. Name the policy (e.g., `Windows 10/11 compliance policy`).
> 5. Under **Compliance settings**, navigate to **System Security** and set the following to **Require**:
>    - `Require a password to unlock mobile devices`
>    - `Require encryption of data storage on device` (This enforces BitLocker)
> 6. Under **Assignments**, click **+ Add all users**.
> 7. Review and **Create** the policy.

_...insert your screenshot of the completed "System Security" settings for the compliance policy here..._

### 2.2 Create the Conditional Access Policy
This is the master rule that enforces our compliance policy.

> 👉 **Action:**
> 1. Navigate to the **Microsoft Entra admin center**.
> 2. Go to **Protection** > **Conditional Access**.
> 3. Click **+ New policy**.
> 4. Name the policy (e.g., `Require Compliant Device for Access`).
> 5. Under **Assignments**, set **Users** to **All users**.
> 6. Set **Target resources** to **All cloud apps**.
> 7. Under **Access controls > Grant**, select `Require device to be marked as compliant`.
> 8. **Enable** the policy and click **Create**. *(Note: You may need to disable Security Defaults and exclude your admin account during creation).*

_...insert your screenshot of the completed Conditional Access policy summary screen here..._

---

## 💻 Phase 3: Building and Enrolling the Windows 11 Test Endpoint

A new endpoint was provisioned using Hyper-V to test the configured environment.

### 3.1 Create the Virtual Machine in Hyper-V
> 👉 **Action:**
> 1. Enable the **Hyper-V** feature in Windows.
> 2. Download the **Windows 11 Pro ISO** from the official Microsoft website.
> 3. In Hyper-V Manager, create a **Generation 2** VM.
> 4. In the VM settings, enable **Secure Boot** and the **Trusted Platform Module (TPM)**.
> 5. Set the boot order to prioritize the **DVD Drive** and attach the downloaded ISO file.

_...insert your screenshot of the final Hyper-V settings for the VM here..._

### 3.2 Install Windows 11 and Enroll in Intune
> 👉 **Action:**
> 1. Start the VM and complete the Windows 11 Pro installation.
> 2. During the setup process (OOBE), select **"Set up for work or school"**.
> 3. Sign in with your Microsoft 365 tenant administrator credentials. This action joins the device to Entra ID and triggers the automatic Intune enrollment.

_...insert your screenshot of the "Access work or school" settings page inside the completed VM here..._

---

## ✅ Phase 4: Verification and Remediation

The final phase involved testing the policies and resolving a compliance failure.

### 4.1 Identify the Compliance Issue
> 👉 **Action:**
> 1. Navigate to **Devices** > **All devices** in the Intune admin center.
> 2. Observe that the new VM is listed but has a status of **`Not Compliant`**.
> 3. Click on the device, then go to **Device compliance** to investigate. The report shows a failure on the **BitLocker** encryption setting.

_...insert your screenshot showing the device as "Not Compliant" in the Intune portal here..._

### 4.2 Remediate the Policy
> 👉 **Action:**
> 1. Navigate back to the `Windows 10/11 compliance policy`.
> 2. **Edit** the **Compliance settings**.
> 3. Change the `Require encryption of data storage on device` setting from `Require` to `Not configured`.
> 4. **Review + save** the policy changes.

_...insert your screenshot showing the edited encryption setting in the compliance policy here..._

### 4.3 Verify the Final Compliant Status
> 👉 **Action:**
> 1. From the Intune portal, force a **Sync** on the device.
> 2. From inside the VM, navigate to `Settings > Accounts > Access work or school > Info` and manually trigger a **Sync**.
> 3. After a few minutes, refresh the device page in the Intune portal.
> 4. **Result:** The device status successfully updates to **`Compliant`**, confirming the remediation was successful.

_...insert your screenshot showing the device with a final "Compliant" status in the Intune portal here..._
