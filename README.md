# Hybrid-Security-Platform
Automated hybrid security platform integrating AWS, Wazuh SIEM, Suricata IDS/IPS, OPNsense segmentation, Terraform infrastructure deployment, and scripted user provisioning for centralized monitoring, threat detection, and security operations workflows.

In this repository I will walk through the configuration of the tools, enviorment, detection rules, automation, and attack scenarios.

# SOpnsense-Firewall-Network-Segmentation
In this project I will be updating my opnsense network to have network segmentation. This is the diagram of how the new network will look after the implementation of network segmentation. 

<img width="781" height="518" alt="image" src="https://github.com/user-attachments/assets/c546cc7e-301c-45fc-830a-f5c2356953d8" />

The first thing that must be done is add new internal networks adapters to the firewall. This is done by navigating to the settings and enabling all four network adapters and naming them intnet-client, intnet-server, and intnet-DMZ. Each network will host different machines. As seen in the diagram the client side will hold a windows 10 virtual machine. The server side will hold the Wazuh server and the active directory server. Then the demilitarized zone will hold the kali linux virtual machine. 

Then we will navigate to the kali linux machine and assign the different interfaces. This is done by logging into the opnsense firewall login page and navigating to interfaces and assignments. Then select each of the network adapters(em1, em2, em3) and name them client, server, and DMZ respectively. 

<img width="998" height="355" alt="image" src="https://github.com/user-attachments/assets/cc23f8f2-bd66-4a3a-a611-d5e9a0599a5d" />

Select save and interfaces and the names of each of the subnetworks should be displayed as follows.

<img width="198" height="377" alt="image" src="https://github.com/user-attachments/assets/2bfe8738-6ec0-4e0f-9c0d-9573b223efee" />

Select each individual subnetwork and select enable interface, set all of the ipv4 address to 10.200.10.1/24, 10.200.20.1/24, and 10.200.30.1/24 respectively. Save these settings navigate to the firewall, assign the interfaces as was done in the previous steps. When all networks have been accounted for, the result should look like this.

<img width="718" height="317" alt="image" src="https://github.com/user-attachments/assets/58e6fb6d-6a25-4e86-b9cb-daa6fb5d4e9f" />

We will add rules to all of the networks to simulate real network segmentation. select Firewall -> Rules -> Client, click add(+). 

<img width="1230" height="927" alt="image" src="https://github.com/user-attachments/assets/7f590ba1-793d-4815-88f4-20c0653ab29b" />

For the Client net the rules go as follows: Set Action: Pass, Interface: Client, Protocol: Any, Source: Client net, and Destination: any. Set Action: Pass, Interface: Client, Protocol: Any, Source: Client net, and Destination: Server net. For the DMZ net the rules go as folows: Action: Block, Interface: DMZ, Protocol: IPv4, Source: DMZ net, and Destination: Client net. Action: Block, Interface: DMZ, Protocol: IPv4, Source: DMZ net, and Destination: Server net. Action: Pass, Interface: DMZ, Protocol: IPv4, Source: DMZ net, and Destination: Any. Finally, the Server rules go as follows: Action: Pass, Interface: Server, Protocol: IPv4, Source: Server net, and Destination: Any. 

Now I will add the Winodws 10 virtual machine and the Windows Active Directory Server. Create a new virtual machine with the Windows 10 iso image. I gave my machine 4096 MB and 1 CPU ad secleted skip unintended install. 

<img width="940" height="725" alt="image" src="https://github.com/user-attachments/assets/7138ce8e-26a2-476d-ad97-e912b06985a3" />

Power up the virtual machine and start the configuration process until prompted to activate Windows. Select I dont have a product key -> Windows 10 Pro. Click custom install to install Windows 10 only. Allow that installation to finish.

Now I will install the active directory server to do this download the Winodows 2022 server from the Windows website. Create a new machine and instert the iso image that was downloaded from the Windows website. Click skip unintended installation, next select finish to boot up the virtual machine.

<img width="945" height="717" alt="image" src="https://github.com/user-attachments/assets/7cc49d30-288b-4f70-ba32-4c3cbe1e7fa0" />

When the machine has started go through the configuration that is wanted then when prompted to ask for a version. Select the standard Evaluation Windows Server and select custom install. Wait for the installation to finish.

<img width="1022" height="847" alt="image" src="https://github.com/user-attachments/assets/716fe61d-0cae-4b7c-93de-86c877cbc339" />

When finished it will prompt the user to enter in a password then select finish. 

<img width="1022" height="856" alt="image" src="https://github.com/user-attachments/assets/26dbd1e4-4c6b-44ef-9859-d0fc61c457bc" />

Now we will configure the Wazuh server to allow the collection of logs. Go to the Ubuntu Server page and download this version.

<img width="381" height="218" alt="image" src="https://github.com/user-attachments/assets/4a8293a1-b67e-467a-abbb-2b4c2503ed3b" />

Create a new machine and use the now downloaded Ubuntu Server. Run through the configuration and complete the download. Once that is done the server should look like this.

<img width="801" height="101" alt="image" src="https://github.com/user-attachments/assets/e8af808a-196d-4001-b0f2-a7dd6695b260" />

Now we will install Wazuh. To do this login to the Ubuntu Server and enter these commands curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh and sudo bash wazuh-install.sh -a. 

<img width="800" height="325" alt="image" src="https://github.com/user-attachments/assets/f7ca0213-35f1-4a95-a2ac-d93a0ff93f61" />

Wait for the installation process to finish. Then login to the active directory server navigate to powershell to enter these commands. Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.5-1.msi -OutFile wazuh-agent.msi next msiexec /i wazuh-agent.msi WAZUH_MANAGER="10.200.20.10". To start the agent after installation use this command net start WazuhSvc. Login to Wazuh and check whether or not it is correctly configured.

<img width="1017" height="842" alt="image" src="https://github.com/user-attachments/assets/fd056e3a-f318-4db9-aa8b-a7fe152ff89c" />

We are going to finish setting up the active directory. Therefore, search server manager and select manage. Keep everything as is and select active directory domain services and select add feature. 

<img width="788" height="562" alt="image" src="https://github.com/user-attachments/assets/8c235459-636c-4644-9920-cf40b0c27e21" />

Once the installation has finished. Select promote this server to domain controller. 

<img width="337" height="272" alt="image" src="https://github.com/user-attachments/assets/fd64f7a1-0383-4b33-b67e-2b3ae188f899" />

Click add a new forest as well as name it lab.local. Select next for all options leading to the installation screen. Click install. When the instllation has finished the screen will display two users. Login to the main user that had a \ in the name. Once server manager opens select tools -> active directory users and computers. 

<img width="752" height="527" alt="image" src="https://github.com/user-attachments/assets/0c5ffa08-928e-4434-aded-ed9d6daeb29f" />

We are going to create a new organizational unit called SOC. This is done by right clicking the domain selecting new -> organizational unit. We will go to the newly created unit and select new -> user. Add a name as well as a new password to the user, select create.

<img width="755" height="271" alt="image" src="https://github.com/user-attachments/assets/85f575f3-d1d6-4067-9481-534ad337383c" />

Open the Windows 10 machine then type this PC and click properties. Scroll down to select advanced system settings.

<img width="802" height="630" alt="image" src="https://github.com/user-attachments/assets/d0577ebc-7910-43ef-af1b-7d9e9833fb67" />

Select domain and enter the name(lab.local). It will not work, to make it work go to ethernet -> properties -> IPv4 -> propeties. Enter the IP address of the domain controller into the DNS section. Once this is done repeat the previous step. You will get credentials that need to be entered. Enter administrator and the password that you created. 

<img width="510" height="463" alt="image" src="https://github.com/user-attachments/assets/22e82ff9-551a-4f1f-9962-c3ecd41f8efd" />

Install the Wazuh agent onto the Windows 10 machine as previously stated. After this is done open the Kali Linux VM and ping the IP address of the Windows 10 machine.

<img width="647" height="106" alt="image" src="https://github.com/user-attachments/assets/521f7533-528c-4b6d-b3d5-65f8ee618a1a" />

It will fail because of a rule that we previously configured to not allow the DMZ and Client networks to communicate with one another. To fix this navigate to the search bar and search the IP address of the firewall. Once this is done select Firewall -> Rules -> DMZ. Replace the old rule and allow traffic from the DMZ to the Client network. 

Security Design Decisions
- Added network segmentation creating Client, Server, and DMZ networks to reduce latteral movement of attackers.
- Prevented DMZ communication with the Client and Server networks. This is done to simulate an untrusted external zone.
- Allowed Client and Server communication for authentication and services.
- Designed to reduce latteral movement of attackers if the system is infiltrated.
