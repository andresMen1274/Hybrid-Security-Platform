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

## Security Design Decisions

- Segmented client and security networks to limit lateral movement
- Restricted DMZ access to monitored services only
- Centralized telemetry using Wazuh for unified investigation workflows
- Used Sysmon to increase Windows endpoint visibility

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

## Attack-Simulation
Simulated adversary behaviors within an isolated lab environment to validate detection coverage and monitoring capabilities.

### Simulated Activity
- RDP brute-force attacks
- Network reconnaissance
- Port scanning
- Unauthorized AWS API activity
- Lateral movement simulation

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

## Detection & Investigation
### Detection Examples
- Windows Event ID 4625 failed login detection
- Suricata network scanning alerts
- AWS CloudTrail unauthorized API monitoring
- Wazuh brute-force correlation alerts

### Investigation Workflow
- Centralized telemetry review
- IOC identification
- Source IP correlation
- Alert triage

Now I will like to look at the logs generated as a result of this. Login to the Wazuh dashboard and enter the credintals. Select Modiules -> security Events and enter the number 4625(this number correlates to failed login attempts). 

<img width="1047" height="535" alt="image" src="https://github.com/user-attachments/assets/918492ba-a63c-42ea-8af2-c8db87b0f1e4" />

Open the log to see more detailed information about how the attempt.

<img width="692" height="761" alt="image" src="https://github.com/user-attachments/assets/452896e0-2d4c-4d76-9825-835f14028d49" />


<img width="820" height="698" alt="image" src="https://github.com/user-attachments/assets/4b3b2513-ff99-4177-95b1-220bc340cf32" />

<img width="1652" height="742" alt="image" src="https://github.com/user-attachments/assets/e900c466-c329-4558-b71a-7d94a9359106" />

From the information given we can see the IP address of the Kali Linux machine and the fail login attempts.

## Cloud
Create a free AWS account. Then we will configure a VPC(virtual private cloud) search VPC -> create VPC. Select VPC and more, name the VPC(in my case it is named SIEM-lab-VPC), set number of avaliability zones to 1, number of public subnets to 1, number of private subnets 0, and no NAT gateways. Now after the VPC has been created we will launch our first EC2 instance. Navigate to EC2 -> instance -> launch instances and name the EC2 instance. The OS image I have selected is the AWS 2023 Linux image, t3.micro for instance type, create a new key pair(leave it as RSA encryption), set the VPC to the newly created SIEM-lab-VPC, enable auto assign public IP, allow SSH and only allow connections from My IP, Create a new security group(name it), and launch instance. 

Now we will make sure that the VPC and instance has been created. To do this naviagte to VPC -> your VPCs. To check that the instance has been successfully created by navigating to EC2 -> instances. Important note make sure that the EC2 instance is stopped while it is not in use. 

<img width="1596" height="292" alt="image" src="https://github.com/user-attachments/assets/75688fb7-b3df-4246-966f-53e9b7146130" />

<img width="1602" height="231" alt="image" src="https://github.com/user-attachments/assets/38374ced-ed80-4747-b30d-061c36e04ab6" />

Now I will connect to the instance that I have created. Now using the key that was created we will ssh into the instance. This is done by entering this command.

ssh -i .\<key-name>.pem ec2-user@<> <-- IP address

<img width="841" height="252" alt="image" src="https://github.com/user-attachments/assets/69bd2c72-b04f-4f1b-bb7a-7ebd847f1181" />

Now I will check that everything has been configured correctly by entering these commands to see user, host name, and ip address.

whoami
hostname
ip addr

<img width="1140" height="487" alt="image" src="https://github.com/user-attachments/assets/946a576c-c92b-4401-bfe9-e157500dd251" />

Now to make sure that the instance is updated and configure common tools are configured, enter these commands as follows:
sudo dnf update -y
sudo dnf install -y nmap tcpdump git htop

Now we will generate some logs to make sure that we can view them. This can be done in various ways, but I will do so by running the commands:
curl ifconfig.me
ping -c 4 google.com
sudo nmap -sS localhost

CloudTrail is a service that is provided by AWS which used to record, monitor, and retain account information across AWS infastructre. Since we want to gather logs from each user we will create a trail. To do this navigate to CloudTrail -> create trail -> name the trail and select create trail. 

<img width="1887" height="348" alt="image" src="https://github.com/user-attachments/assets/5803da81-fa99-4bb4-b92c-3377d58c9bb2" />

Now stop the instance and start it again. Then navigate to CloudTrail -> event history and view the logs that show the stopping and starting of the instance. Now that we have a good view on account information we want to monitor Network traffic. To do this we navigate to VPC -> select the VPC that you created -> open flow logs tab -> select create flow log. Name the flow logs, set to collect all logs, 10 minute agregation, send to CloudWatch logs, create and use a new service role, and AWS defalut format. 

<img width="1366" height="647" alt="image" src="https://github.com/user-attachments/assets/9ca1d5b3-e8c3-40c3-8599-07270ea756a8" />

To make sure that logs are working naviagte to CloudWatch -> logs -> log management -> select your group. Then view the logs that have been generated. 

<img width="1567" height="258" alt="image" src="https://github.com/user-attachments/assets/8e6b67ca-a185-45e2-92fa-15c10fa8f560" />

Now that we have confirmed that the logs are being forwarded correctly we will connect this to our home lab. To do this start the wazuh server and ssh into it using the command ssh wazuh@10.200.20.10. After that has been completed then enter the commands:

sudo apt update
sudo apt install awscli -y

What these commands do is update the Wazuh server and add the aws extension to forward logs to the Wazuh server. Now we will create a IAM user that will be used for AWS logs. naviagte to IAM -> IAM users -> create user. Then name the user, select attach policies directly(attach the policy AmazonS3ReadOnlyAccess), and then create user. After it has been correctly created then select security credentials and create access key. Select Command Line Interface(CLI) then select next -> create access key. Make sure that when the Access and secret keys are displayed that you save them becuase they will not be shown again. After this step has been done go back to the wazuh server and enter aws configure. Enter in the acces and secret keys. Input the region. modify the ossec.conf file with this command:

sudo nano /var/ossec/etc/ossec.conf

Add this block at the bottom of the configuration file.

```
<wodle name="aws-s3">
  <disabled>no</disabled>
  <interval>10m</interval>
  <run_on_start>yes</run_on_start>

  <bucket type="cloudtrail">
    <name>aws-cloudtrail-logs-190986995757-062883f2</name>
    <aws_profile>default</aws_profile>
  </bucket>
</wodle>
```

These commands update the permissions so that it is able to access the profile. Then viewing the logs for any configuration errors.

sudo cp -r ~/.aws /root/

sudo chown -R root:root /root/.aws

sudo systemctl restart wazuh-manager

sudo tail -f /var/ossec/logs/ossec.log
