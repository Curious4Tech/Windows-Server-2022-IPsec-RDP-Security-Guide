# Windows-Server-2022-IPsec-RDP-Security-Guide
Step-by-step guide to configure **`IPsec Policies`** and secure **`Remote Desktop (RDP)`** on Windows Server. Includes **`IPsec setup`**, custom **`RDP port`** configuration, and best security practices.


---

# Configuring IPsec Policies Between Windows Server 2022 and Windows 11  

This guide provides a **step-by-step process** to configure **IP Security (IPsec)** Policies for secure communication between a **Windows Server 2022** machine and a **Windows 11** client. IPsec policies allow granular control over encrypted communication.

---

## Table of Contents  
1. [Step 1: Configure IPsec Policy on Windows Server 2022](#step-1-configure-ipsec-policy-on-windows-server-2022)  
2. [Step 2: Import the IPsec Policy on Windows 11](#step-2-import-the-ipsec-policy-on-windows-11)  
3. [Step 3: Assign Policies](#step-3-assign-policies)  
4. [Verification](#verification)  
5. [How to Change the RDP Port on Windows Server or Windows 11](#how-to-change-the-rdp-port-on-windows-server-or-windows-11)  
6. [Security Tips for RDP](#security-tips-for-rdp)  
7. [Notes](#notes)  

---

## Step 1: Configure IPsec Policy on Windows Server 2022  

### 1. Open Group Policy Management or Local Security Policy  
   - Press `Win + R` to open the Run dialog.  
   - Type `secpol.msc` and press Enter.  
   - This opens the **Local Security Policy**.

### 2. Navigate to IP Security Policies  
   - Go to:  
     **IP Security Policies on Local Computer**.

### 3. Create a New IP Security Policy  
   - Right-click **IP Security Policies on Local Computer** > Select **Create IP Security Policy**.  
   - Follow the wizard:  
     - Provide a **name** for the policy.  
     - Ensure **"Activate the default response rule"** is **checked**.  

### 4. Manage IP Filter Lists and Filter Actions  
   - After creating the policy:  
     - Right-click the new policy and select **Manage IP filter lists and filter actions**.  

### 5. Create IP Filters  
   - Define the IP addresses for both machines:  
     - **Source**: IP of **Windows Server 2022**.  
     - **Destination**: IP of **Windows 11**.  
   - Apply the filters to the policy.  

### 6. Set Authentication and Action  
   - Right-click the configured filter and set the following:  
     - **Authentication Method**:  
       - Choose either **Pre-shared key** (less secure) or **Certificates** (recommended for production).  
     - **Action**:  
       - Set to **Require Secure Communication** or **Encrypt**.  

---

## Step 2: Import the IPsec Policy on Windows 11  

### 1. Open Local Security Policy  
   - Press `Win + R`, type `secpol.msc`, and press Enter.  

### 2. Import or Recreate the Policy  
   - Navigate to **IP Security Policies**.  
   - Right-click **IP Security Policies**:  
     - Import the policy exported from the Windows Server.  
     - Alternatively, recreate the policy using the same steps outlined in [Step 1](#step-1-configure-ipsec-policy-on-windows-server-2022).  

---

## Step 3: Assign Policies  

### Assign Policies on Both Machines  
   - On **Windows Server 2022** and **Windows 11**:  
     - Right-click the IPsec policy and select **Assign**.  

### Verify Policy Assignment  
   - Open Command Prompt as Administrator.  
   - Run the following command to verify the active IPsec policy:  

     ```bash
     netsh ipsec static show policy
     ```

---

## Verification  

To confirm that IPsec is securing communication between the machines, follow these steps:

1. Open Command Prompt on **both machines** as Administrator.  
2. Run the following command to view active IPsec connections:  

   ```bash
   netsh ipsec dynamic show all
   ```

3. Ensure that IPsec-secured packets are being transmitted.

---

## How to Change the RDP Port on Windows Server or Windows 11  

### Key Points:  
- **Port Type**: TCP (default).  
- **Default RDP Port**: 3389.  
- **Customizing the Port**: You can change this port for security reasons (obscurity) by modifying the registry.  

### Steps to Change the RDP Port  

1. **Open the Registry Editor**:  
   - Press **`Win + R`**, type **`regedit`**, and press Enter.  

2. **Navigate to the Registry Key**:  
   ```  
   HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp  
   ```

3. **Find and Modify the Port**:  
   - Locate the **PortNumber** (DWORD) value.  
   - Double-click **PortNumber**.  
   - Select **Decimal** and enter the new port number (e.g., **3390**).  

4. **Restart the RDP Service or Reboot the Machine**:  
   Run the following command in Command Prompt as Administrator:  

   ```bash
   net stop termservice && net start termservice
   ```

5. **Update Firewall Settings**:  
   - In **Windows Firewall**, create a **new inbound rule** to allow traffic through the **custom RDP port**.  

6. **Accessing RDP with a Custom Port**:  
   - When connecting to the server using the Remote Desktop Client, specify the custom port:  

     ```
     <IP Address>:<Port>
     Example: 192.168.1.10:3390  
     ```

---

## Security Tips for RDP  

1. **Change the Default Port**:  
   - Changing from **3389** helps reduce exposure to automated attacks.  

2. **Enable Network Level Authentication (NLA)**:  
   - NLA ensures only authenticated users can establish an RDP session.  

3. **Restrict RDP Access**:  
   - Use **Windows Firewall** to restrict RDP access to specific IP addresses.  

4. **Implement Multi-Factor Authentication (MFA)**:  
   - MFA adds an additional layer of security for remote access.  

5. **Monitor Logs**:  
   - Regularly check logs for **suspicious login attempts** or failed RDP connections.  

---

## Notes  

- **Pre-shared Key**:  
  - Easy to configure but **less secure**. Use certificates in production environments.  
- **IPsec Rule Matching**:  
  - The rules must match **exactly** on both machines for communication to work.  
- **Firewall Troubleshooting**:  
  - If IPsec fails to work, temporarily disable the firewall to isolate issues.  

---

## Conclusion  

By following this guide:  
1. You can **configure IPsec policies** for secure communication.  
2. Learn how to **customize the RDP port** to enhance security.  

If you encounter any issues, open an **issue** in this repository.
