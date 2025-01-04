<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)

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


<h2>Actions and Observations</h2>

<p>
<img src="https://github.com/user-attachments/assets/43e1f4cd-3684-4d03-b713-978ddab01759" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
On Microsoft Azure you should click on Create Resource Group
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/510de9fb-ff5c-4a16-9564-6919cdda6987" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Name it test lab and click Review+Create   
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/dec4b2db-86c7-4b7e-9e3d-dfd52a746d5f"/>
</p>
<p>
Go back to the Home Page of Microsoft Azure and click on Virtual machines   
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/73bea4dd-c86f-4da2-8fb4-27417cb66c95"80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click on create and a drop down menu will drop and click on Azure virtual machine
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/a2948452-804e-4b11-b862-953cd0bc2875" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
make sure it is in the resource group you created, name it windows-vm
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/25bf79f4-3012-4683-b2a6-4af456e794a9" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
under image click windows 10 pro
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/156447ec-b76b-4f72-a482-07453f6df333" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
for the size anything with 2 or more VCPU should be fine anything lower might cause the virtual machine to run slower   
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/d2b0bf08-fe3c-4733-8d3d-165e5dad648e" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
under licensing click it if you dont it wont allow you to create the virtual machine    
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/8cdde398-0493-4d7d-ae59-99e9a628865f" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
click next until you get to networking, then next to virtual networks click create and name it lab1-Vnet than ok and you can click create and review and wait for it to delploy
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/9937b4a2-ee9e-4860-a0b0-7de247867ff9" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go back to the where you created the virtual machine click create, the dropbox will pop up and then click azure virtual machine
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/dc7a24d5-aacc-440b-900b-7e8d09b87ce2" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
make sure its in the same resource group as the windows-vm, name it linux-vm
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/683b89c9-760d-4a6a-80dd-6369346ab6bf" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
by images select ubuntu server 22.04
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/836e5364-12e3-4d55-a719-224c69d888b6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
size anything with 2 or more Vcpus is good, for authentication type select password and scroll down and make up a user and password you'll remember
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/5e7be805-2c7b-4fae-a386-c0d305c4430c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
click next until you get to networking and make sure that the it is in the same virtual network as the other vm and then click create+review and wait for it to deploy
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/dbbfe71f-882a-440d-b96b-45b6a9be2255" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
once deployed go to the virtula machines and copy the public IP address for the windows virtual machine and on the bottom where it says search click on it and type RDP and click it 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/54d1d911-8a70-49df-827a-3c402726edbd" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
enter the public IP address into where it says computer and then click connect
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/f75f433e-ab7d-4530-8abc-ebf73d1085d8" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
then a windows is going to pop up and click more choices and then use different account and enter the credentials you entered for the windows virtual machine and yes to the window that follows and you should be loading into the vm and then click allow
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/22f54566-3c85-4e31-8072-2b34eba327b2" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
then copy and paste this wire shark link https://www.wireshark.org/#downloadLink and download the appropriate one for your computer 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/8face2af-6988-4c0f-99a9-2fa19a648540" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
click on the download thats next to the puszzle piece in the upper right hand corner and click open file
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/9b7ee364-7f1f-432a-a090-ce75f5674c4f" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
then click it at the bottom of the screen then click next and you dont have to change anything so just click next until you hit install and contine clicking next until you finish
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/6bddb2bc-677e-4b97-94c6-8dce6cd2a837" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go to the bottom of the screen left hand side and click the search, type in wireshark open it
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/af8e69f9-0a6a-4370-ba62-8ea033f69670" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
click on the ethernet and there should be a little line getting drawn by it showing its picking up traffic
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/cfae0f7b-bcbc-4c47-880d-da97c8ab3642" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
click on the tab under the blue fin and type icmp to filter for only icmp traffic
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/20c72849-adcc-4721-9d0e-9f8239848910" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go to the bottom left hand side click search type in windows powershell and click open 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/e9c44683-d88e-4a95-9300-c638712f4ab0" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go back to microsoft azure and click on the linux vm you created 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/1d929719-1a5e-4275-8d2d-1078c2858ae5" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
scroll down on the window that opens and copy the private IP address
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/be51a9eb-28db-483c-9eac-7c78bfcbbfae" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
in the windows powershell you opened earlier type in ping the private ip address you got from the linux vm example ping 10.0.0.5
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/09dee767-82d9-422b-8214-41fe36e44877" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
then minimize the powershell window and open wireshark if its not on the screen and see that the ping went through 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/843c574f-5a92-4e3f-a807-155dd20f3162" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go back to the windows powershell and type in ping linux private ip address and -t example ping 10.0.0.5 -t the -t flag just makes the ping to go on forever until it is stopped by a firewall or the user stops it 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/b81a4c25-ae53-48f3-9d20-ba3a4cca7eba" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go back to microsoft azure and click on the linux virtual machine then click on networking, then network settings
</p>
<br />

<p>
<img src="!https://github.com/user-attachments/assets/b64171a4-1e3f-4e3f-aa52-05aacc812c52" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
then click on network security settings 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/19184f99-67da-4cd0-9a69-cfd09e0f525a" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
then click inbound security rules
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/b060d1f7-d92d-4994-ac24-2f9237348f51" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
click add then go to destination port and add an astrick for protocol click icmpv4, action deny and for priority change it to 290 so it is the highest priority of the other rules and gets processed first and click add
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/6466a15e-f5d2-466a-9ee9-87e8094f7e80" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go back to the wire shark on the vm and windows powershell and you will see the constant ping has stopped due to the firewall rule you have configured
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/641909e8-a9e7-42f1-86af-7654ff0ed009" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go back to where you created the rule and you can click the trash by the rule you created and it gets deleted
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/128a7fa6-0172-4eef-8c7e-ca898843c3b1" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
you can go back to windows powershell on the windows-vm and see the pinging started again
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/49a6e435-dfd9-48d8-b445-cc89f4e2c3a0" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go to the windows powershell and type ipconfig /renew and all it does is it gets a new ip address 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/079d5f80-5134-4eeb-a911-e05b31beacd6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go back to wireshark and observe the DHCP traffic that ipconfig /renew created 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/fa4e0225-6ac9-48e2-8aec-4ce3e5dce218" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
in windows powershell type in ssh the username you choose for the windows virtual machine @ the private ip address example ssh labuser@10.0.0.5 and then type in yes to the question it asks you later, then type in the password you set for the virtual machine you wont be able to see it being typed but it is once your done typing click enter and you can type hostname to tell if you have successfully logged into the linux virtual machine
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/9014c637-1266-4a4c-a7a6-74b604d9212f" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
go back to wireshark and see all the ssh traffic that was captured anytime you type a letter there is a new entry that is captured and if you try to inspect the traffic it is encrypted so you cant see but on telnet you can since it sends information in plaintext
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/ef26f587-4025-45df-be2b-68c09970a5c1" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
now type in where you filter traffic tcp.port==3389 and that is rdp traffic since RDP uses tcp ports and the port it uses is 3389
</p>
<br />

