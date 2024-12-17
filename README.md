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

![image](https://github.com/user-attachments/assets/c13a87d8-12cd-4083-a7e0-4a0bdd35e2e5)

   - This opens the **Local Security Policy**.

### 2. Navigate to IP Security Policies  
   - Go to:  **`IP Security Policies on Local Computer`**.

![image](https://github.com/user-attachments/assets/e623d117-54ff-44fe-bd49-f49619848ef8)

### 3. Create a New IP Security Policy  
   - Right-click **`IP Security Policies on Local Computer`** > Select **`Create IP Security Policy`**.

![image](https://github.com/user-attachments/assets/4898dd66-a138-4952-9aa8-8ca059540ae6)

   - Follow the wizard:  
     - Provide a **`name`** for the policy.

![image](https://github.com/user-attachments/assets/b9290352-b857-43ca-a998-83fff902d6a1)
    
   ### 3. 1. **Authentication Method**:  
   - Choose either **Pre-shared key** (less secure) or **Certificates** (recommended for production).  
     - **Action**:  
       - choose **`Certificate from this CA`**, check **`Enable certificate to account mapping`** and then click on **`Browse`**.

   ![image](https://github.com/user-attachments/assets/7ee967de-40b6-4183-8b3d-ef4000759d49)

   - In the pop-pup that is going to open, just click on **`OK`**.

![image](https://github.com/user-attachments/assets/23c023e5-fd96-4bcf-91cc-76d2e65bfca5)

   - Ensure **`Activate the default response rule`** is **`checked`**.  

![image](https://github.com/user-attachments/assets/df4a3a8e-1ee9-4050-bc24-4357b03f742d)

### 4. Manage IP Filter Lists and Filter Actions  
   - After creating the policy:  
     - Right-click the new policy and select **`Manage IP filter lists and filter actions`**.  

![image](https://github.com/user-attachments/assets/afda015a-5e05-4ff5-adfb-3599918c9382)

### 5. Create IP Filters  
   - Define the IP addresses for both machines:  
     - **`Source`**: IP of **Windows Server 2022**.  
     - **`Destination`**: IP of **Windows 11**.

![image](https://github.com/user-attachments/assets/930f4a07-3eb0-45ad-9594-5cacfdb98f18)

   - Apply the filters to the policy.  

![image](https://github.com/user-attachments/assets/54ad2842-b47d-4169-b8c6-bfa99299e717)

 - **Action**:  
       - Set to **Require Secure Communication** or **Encrypt**.  

 ![image](https://github.com/user-attachments/assets/d0f8bc0e-8beb-463d-a40f-faa14b08270b)

### 6. Set Authentication and Action(Optional if not set in 3. 1) 
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
   - On **`Windows Server 2022`** and **`Windows 11`**:  
     - Right-click the IPsec policy and select **`Assign`**.  

![image](https://github.com/user-attachments/assets/726b6755-5c09-4b9b-9de8-6b07b29d3d50)

### Verify Policy Assignment  
   - Open Command Prompt as Administrator.  
   - Run the following command to verify the active IPsec policy:  

     ```bash
     netsh ipsec static show policy all 
     ```

![image](https://github.com/user-attachments/assets/36e608af-6844-41e7-b6d5-d4c220318353)

---

## Verification  

To confirm that IPsec is securing communication between the machines, follow these steps:

1. Open Command Prompt on **`both machines`** as Administrator.  
2. Run the following command to view active IPsec connections:  

   ```bash
   netsh ipsec dynamic show all
   ```

![image](https://github.com/user-attachments/assets/7de7d092-a513-4647-81e5-3cf13f335da3)

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

![image](https://github.com/user-attachments/assets/787cc5ed-aaa1-4072-9b6a-f2650b278ada)

2. **Navigate to the Registry Key**:  
   ```  
   HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp  
   ```

3. **Find and Modify the Port**:  
   - Locate the **`PortNumber`** (DWORD) value.  
   - Double-click **`PortNumber`**.  
   - Select **`Decimal`** and enter the new port number (e.g., **`1443`**).  

![image](https://github.com/user-attachments/assets/3b6204f6-da03-4ece-94d4-795804786bf8)

4. **Restart the RDP Service or Reboot the Machine**:  
   Run the following command in Command Prompt as Administrator:  

  
   1. Stop the service
  ```bash
   net stop termservice
  ```
![image](https://github.com/user-attachments/assets/5e05afc1-66e5-490f-a1ad-c3cb516b2b07)

  2. Start the service
  ```bash
   net start termservice
  ```
![image](https://github.com/user-attachments/assets/82227f82-26fe-4323-975b-440d364d13c4)


6. **Update Firewall Settings**:  
   - In **`Windows Firewall`**, create a **`new inbound rule`** to allow traffic through the **`custom RDP port`**.  

![image](https://github.com/user-attachments/assets/4b74a9c5-2c22-4206-a0d4-93b73ac169df)

7. **Accessing RDP with a Custom Port**:  
   - When connecting to the server using the Remote Desktop Client, specify the custom port:  

     ```
     <IP Address>:<Port>
     Your username  
     ```

![image](https://github.com/user-attachments/assets/03c0c8e4-ba5f-453a-8640-18d6bd613fdc)

- Provide your password and follow the wizard.

![image](https://github.com/user-attachments/assets/ff146cb8-abb3-47ff-8a02-dc196f1e6b90)

- Congratulation, you now successfully login.

![image](https://github.com/user-attachments/assets/e29bb72a-e576-45c7-9c46-db52e576c1c9)

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
