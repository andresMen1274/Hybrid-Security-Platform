# Hybrid-Security-Platform
Automated hybrid security platform integrating AWS, Wazuh SIEM, Suricata IDS/IPS, OPNsense segmentation, Terraform infrastructure deployment, and scripted user provisioning for centralized monitoring, threat detection, and security operations workflows.

In this repository I will walk through the configuration of the tools, enviorment, detection rules, automation, and attack scenarios.

# Opnsense-Firewall-Setup
This is a project in which I set up a virtual environment using a Wide Area Network(WAN) and Local Area Network(LAN) and simulate a network connection between them. Furthermore, I configure a firewall between this connection and follow security procedures.

After downloading Virtual Box and Kali Linux I configure a Wide Area Network(WAN) and Local Area Network(LAN) and simulate a network connection between them. Network Adapter one is used to simulate the WAN connection. Then adapater two is used to simulate a LAN connection.

<img width="688" height="366" alt="image" src="https://github.com/user-attachments/assets/9f1c844a-669a-4091-b3db-938e700fbd69" />

<img width="692" height="367" alt="image" src="https://github.com/user-attachments/assets/55bf9045-a1ba-469a-8792-03237989b362" />

Using the downloaded Opnsense file we will start the virtual machine with this disk file.

<img width="808" height="496" alt="image" src="https://github.com/user-attachments/assets/608b05f5-50ca-4e86-8a87-60fb82e04ad8" />

Using the defalut username installer and password opnsense. We are able to open the opnsense download menu.

<img width="732" height="496" alt="image" src="https://github.com/user-attachments/assets/885e27cb-3431-43e4-8a7b-787832a5d77f" />

After successfully downloading the firewall we remove the firewall disk and reboot the system. We will now login as a root user and assign the interfaces em0 will serve as the WAN interface and em1 will serve as the LAN interface. I will not configure the IP address as defalut one is given.

<img width="721" height="492" alt="image" src="https://github.com/user-attachments/assets/80853861-b6d9-40fa-bfbb-a9234fa5c772" />

Next we will boot up kali linux and ping to the LAN interface to check that the connection was configured correctly.

<img width="918" height="731" alt="image" src="https://github.com/user-attachments/assets/fe44590f-da46-4242-8d80-a7ad670b0414" />

Then I will open the web browser and look up the defalut IP that was given and login to opnsense and check the status of the system. Then I go to firmware and check the status of the system and give updates to the system. 

<img width="1272" height="921" alt="image" src="https://github.com/user-attachments/assets/d7f00c16-d0c1-4da2-94d7-625338ce195a" />

