<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Setting up Wireshark and filtering for traffic](https://youtu.be/XhEehgzrvAQ)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

1. **Create a Security Group**
   - In Azure, create a Security Group to house your virtual machines.

2. **Create a Windows 10 Virtual Machine**
   - Set up a Windows 10 VM in Azure.
   - Navigate to the **Networking** tab and create a Virtual Network before clicking **Review + Create**.
   - Deploy the VM.

3. **Create a Linux 22.04 Virtual Machine**
   - Create a Linux 22.04 VM.
   - Ensure it uses the same Virtual Network as the Windows VM by selecting it in the **Networking** tab during setup.

4. **Connect to the Windows VM**
   - Obtain the **Public IP address** of the Windows VM from Azure.
   - Use **Remote Desktop Protocol (RDP)** to connect to the VM.

5. **Install Wireshark on the Windows VM**
   - Log into the Windows VM.
   - Visit [Wireshark](https://www.wireshark.org/#downloadLink) and download the appropriate version.
   - Install and open Wireshark.
   - Click the **blue fin icon** in the top-left to start monitoring traffic.

6. **Monitor ICMP Traffic**
   - In Wireshark, filter for **ICMP traffic**.
   - Retrieve the **Private IP address** of the Linux VM from Azure.
   - Open **Windows PowerShell** on the Windows VM and type:  
     ```bash
     ping [Linux Private IP Address]
     ```
     Example:  
     ```bash
     ping 10.0.0.4
     ```

7. **Continuous Ping**
   - In PowerShell, type:  
     ```bash
     ping [Linux Private IP Address] -t
     ```
     Example:  
     ```bash
     ping 10.0.0.5 -t
     ```

8. **Create a Rule to Block ICMP Traffic**
   - In Azure, go to the Linux VM's **Networking** section.
   - Under **Network Security Groups**, click **Inbound Security Rules** > **Add**.
   - Configure the following settings:
     - **Destination Port Ranges:** `*` (any port)
     - **Protocol:** `ICMPv4`
     - **Action:** Deny
     - **Priority:** 290
   - Click **Apply**.

9. **Verify and Remove the Rule**
   - Return to the Windows VM and verify the ping has stopped.
   - In Azure, delete the rule by clicking the trash icon.

10. **Monitor DHCP Traffic**
    - In Wireshark, filter for **DHCP traffic**.
    - In PowerShell on the Windows VM, type:  
      ```bash
      ipconfig /renew
      ```
    - Observe the traffic in Wireshark.

11. **Monitor SSH Traffic**
    - In Wireshark, filter for **SSH traffic**.
    - In PowerShell, type:  
      ```bash
      ssh [Linux VM Username]@[Linux Private IP Address]
      ```
      Example:  
      ```bash
      ssh labuser@10.0.0.5
      ```
    - Enter the password (it won’t display as you type).  
    - Once connected, type:  
      ```bash
      hostname
      ```
    - Observe the SSH traffic in Wireshark.

12. **Monitor RDP Traffic**
    - In Wireshark, filter for **RDP traffic**:  
      ```bash
      tcp.port==3389
      ```
    - Observe the continuous stream of RDP traffic.

---


<h2>Creating the Virtual Machines</h2>

<p>
<img src="https://github.com/user-attachments/assets/43e1f4cd-3684-4d03-b713-978ddab01759" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In Microsoft Azure, start by selecting 'Create Resource Group' to organize and manage your resources effectively within a defined logical group
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/510de9fb-ff5c-4a16-9564-6919cdda6987" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Name the resource group 'Test Lab,' then click 'Review + Create' to finalize the setup.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/dec4b2db-86c7-4b7e-9e3d-dfd52a746d5f"/>
</p>
<p>
Return to the Microsoft Azure Home Page and select 'Virtual Machines' from the options
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/73bea4dd-c86f-4da2-8fb4-27417cb66c95"80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click on 'Create,' and from the drop-down menu, select 'Azure Virtual Machine.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/a2948452-804e-4b11-b862-953cd0bc2875" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Ensure the virtual machine is placed in the resource group you created earlier. Name the virtual machine 'windows-vm
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/25bf79f4-3012-4683-b2a6-4af456e794a9" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Under the 'Image' section, select 'Windows 10 Pro.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/156447ec-b76b-4f72-a482-07453f6df333" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
For the size, choose an option with 2 or more vCPUs. Selecting fewer may cause the virtual machine to run slower
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/d2b0bf08-fe3c-4733-8d3d-165e5dad648e" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Under 'Licensing,' make sure to check the box. If you don't, you won't be able to create the virtual machine.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/8cdde398-0493-4d7d-ae59-99e9a628865f" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click 'Next' until you reach the 'Networking' section. Then, next to 'Virtual Networks,' click 'Create.' Name the network 'lab1-Vnet,' then click 'OK.' After that, proceed to click 'Create' and 'Review.' Wait for the deployment to complete.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/9937b4a2-ee9e-4860-a0b0-7de247867ff9" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to the section where you created the virtual machine. Click 'Create' again, and a dropdown menu will appear. From the dropdown, select 'Azure Virtual Machine.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/dc7a24d5-aacc-440b-900b-7e8d09b87ce2" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Ensure that the virtual machine is placed in the same resource group as the 'windows-vm'. Name the new virtual machine 'linux-vm
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/683b89c9-760d-4a6a-80dd-6369346ab6bf" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Under 'Image', select 'Ubuntu Server 22.04
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/836e5364-12e3-4d55-a719-224c69d888b6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
For the size, choose any option with 2 or more vCPUs. For Authentication Type, select 'Password' and scroll down to create a user and password you'll remember.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/5e7be805-2c7b-4fae-a386-c0d305c4430c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click 'Next' until you reach the Networking section. Ensure that the virtual machine is in the same virtual network as the other VM. Then, click 'Create + Review' and wait for the deployment to complete.
</p>
<br />

<h2>Connecting to the Virtual machine</h2>

<p>
<img src="https://github.com/user-attachments/assets/dbbfe71f-882a-440d-b96b-45b6a9be2255" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once the virtual machine is deployed, go to the 'Virtual Machines' section and copy the public IP address for the Windows virtual machine. Then, at the bottom where it says 'Search,' click on it, type 'RDP,' and select it.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/54d1d911-8a70-49df-827a-3c402726edbd" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Enter the public IP address into the field labeled 'Computer' and then click 'Connect.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/f75f433e-ab7d-4530-8abc-ebf73d1085d8" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
A window will pop up—click 'More choices,' then select 'Use a different account.' Enter the credentials you set up for the Windows virtual machine. When prompted with a confirmation window, click 'Yes.' You should now be logging into the VM. Finally, click 'Allow' when prompted.
</p>
<br />

<h2>Installing Wireshark</h2>

<p>
<img src="https://github.com/user-attachments/assets/22f54566-3c85-4e31-8072-2b34eba327b2" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Copy and paste this Wireshark link into your browser: https://www.wireshark.org/#downloadLink. Download the version that corresponds to your computer's operating system.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/8face2af-6988-4c0f-99a9-2fa19a648540" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click on the download icon located next to the puzzle piece in the upper-right corner of your browser. Once the download is complete, click 'Open File' to begin the installation process.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/9b7ee364-7f1f-432a-a090-ce75f5674c4f" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
After clicking the file at the bottom of the screen, select 'Next' to begin the installation. You don't need to modify any settings, so simply keep clicking 'Next' until you reach the 'Install' button. Then continue clicking 'Next' until the installation is complete.
</p>
<br />

<h2>Filtering for ICMP traffic</h2>

<p>
<img src="https://github.com/user-attachments/assets/6bddb2bc-677e-4b97-94c6-8dce6cd2a837" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
At the bottom left of the screen, click on the search bar. Type 'Wireshark' and open the application.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/af8e69f9-0a6a-4370-ba62-8ea033f69670" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click on the Ethernet option in Wireshark, and you should see a small line being drawn next to it, indicating that it's picking up traffic.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/cfae0f7b-bcbc-4c47-880d-da97c8ab3642" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click on the tab under the blue fin icon and type 'icmp' to filter for only ICMP traffic.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/20c72849-adcc-4721-9d0e-9f8239848910" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go to the bottom left-hand side, click search, type in 'Windows PowerShell,' and click open.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/e9c44683-d88e-4a95-9300-c638712f4ab0" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to Microsoft Azure and click on the Linux VM you created.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/1d929719-1a5e-4275-8d2d-1078c2858ae5" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Scroll down on the window that opens and copy the private IP address.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/be51a9eb-28db-483c-9eac-7c78bfcbbfae" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the Windows PowerShell you opened earlier, type in: 
   ping <private IP address> 
example: ping 10.0.0.5
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/09dee767-82d9-422b-8214-41fe36e44877" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Minimize the PowerShell window and open Wireshark if it's not already on the screen. You should be able to see that the ping went through by checking the captured traffic.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/843c574f-5a92-4e3f-a807-155dd20f3162" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to the Windows PowerShell and type in:
   ping <Linux private IP address> -t
example: ping 10.0.0.5 -t
The -t flag makes the ping continue indefinitely until it is either stopped by a firewall or manually interrupted by the user.
</p>
<br />

<h2>Configuring the Firewall</h2>

<p>
<img src="https://github.com/user-attachments/assets/b81a4c25-ae53-48f3-9d20-ba3a4cca7eba" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to Microsoft Azure, click on the Linux virtual machine, then navigate to the 'Networking' section and click on 'Network settings.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/b64171a4-1e3f-4e3f-aa52-05aacc812c52" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Then, click on 'Network security settings.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/19184f99-67da-4cd0-9a69-cfd09e0f525a" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Then, click on 'Inbound security rules.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/b060d1f7-d92d-4994-ac24-2f9237348f51" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click 'Add,' then under 'Destination port range,' add an asterisk. For 'Protocol,' select 'ICMPv4.' Set the 'Action' to 'Deny.' For 'Priority,' change it to 290, ensuring it has the highest priority over other rules. Finally, click 'Add.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/6466a15e-f5d2-466a-9ee9-87e8094f7e80" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to Wireshark on the VM and the Windows PowerShell, and you'll notice that the constant ping has stopped. This is due to the firewall rule you configured, which blocked the ICMP traffic.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/641909e8-a9e7-42f1-86af-7654ff0ed009" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to where you created the rule. You can delete it by clicking the trash icon next to the rule you created, and it will be removed.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/128a7fa6-0172-4eef-8c7e-ca898843c3b1" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
You can go back to Windows PowerShell on the Windows VM, and you will see that the pinging has started again.
</p>
<br />

<h2>Filtering for DHCP traffic</h2>

<p>
<img src="https://github.com/user-attachments/assets/49a6e435-dfd9-48d8-b445-cc89f4e2c3a0" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go to Windows PowerShell and type ipconfig /renew. This command simply renews your IP address, assigning you a new one.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/079d5f80-5134-4eeb-a911-e05b31beacd6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to Wireshark and observe the DHCP traffic generated by the ipconfig /renew command.
</p>
<br />

<h2>Filtering for SSH traffic</h2>

<p>
<img src="https://github.com/user-attachments/assets/fa4e0225-6ac9-48e2-8aec-4ce3e5dce218" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In Windows PowerShell, type the following command to SSH into the Linux virtual machine:
   ssh <username>@<private_ip_address>
example: ssh labuser@10.0.0.5
Type 'yes' when prompted, then enter the password you set for the Linux virtual machine. You won't see the password as it's typed, but it will be entered. Afterward, press Enter. Once logged in, you can type hostname to confirm a successful connection to the Linux VM.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/9014c637-1266-4a4c-a7a6-74b604d9212f" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to Wireshark and observe the SSH traffic that was captured. Every time you type a letter during the SSH session, a new entry is logged. You will notice that the traffic is encrypted, meaning you cannot see the actual characters being typed. However, with protocols like Telnet, you would be able to see the data in plaintext because Telnet does not encrypt its traffic.
</p>
<br />

<h2>Filtering for RDP traffic</h2>

<p>
<img src="https://github.com/user-attachments/assets/ef26f587-4025-45df-be2b-68c09970a5c1" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now, type tcp.port==3389 in the Wireshark filter bar to filter for RDP traffic. RDP (Remote Desktop Protocol) uses TCP port 3389, so this filter will show only the RDP-related packets.
</p>
<br />

