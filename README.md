# SIEM-Analysis-Lab
SIEM Analysis lab made in Microsoft Azure environment utilizing log aggregation collection to a third party API to track failed login attempts to a honeypot VM.

<h1>SIEM Analysis Lab in Azure</h1>

<h2>Description</h2>
<p>This cybersecurity lab project consists of using Microsoft Sentinel's SIEM service,Security Center, Azure Virtual Machine as the the honeypot with an open Network Security group to allow inbound access to entice attackers' activity.The PowerShell script collects the VM's Windows Event Viewer- Event Log Error code (4625) alerts that are failed login attempts. The script then automates the logs to be parsed by a third party API (ipgeolocation.io) to provide geolocation of the IP address from the failed login attempts; (longitutde and latitude coordinates). The API's data is then ingested into a custom table in Azure Log Analytics which ingests and parses the failed RDP log in attempts and plots to a Workbook in Microsoft Sentinel as a real-time live map with coordinates of the attacker's IP address, username, country and state. The logs are filtered based on labels for analytical data and alerts based on the error code of a failed log in attempt to the VM.   /p>

  ![diagram_SIEM_lab](https://github.com/brireyn/brireyn/assets/96150916/a15c2ef9-98ac-4dde-94bf-02713dfe2d6b)

<h2>Languages and Utilities Used</h2>
<b>PowerShell</b>
<b>Remote Desktop Protocol</b>
<b>ipgeolocation.io API</b>

<h2>Environments Used </h2>
<b>Microsoft Sentinel</b>
<b>Microsoft Log Analytics</b>
<b>Azure Virtual Machine</b>

<h2>Lab walk-through:</h2>

<h3>Step 1</h3>
Create the honeypot for the attackers to attack. Create a Virtual Machine. 

![step1](https://github.com/brireyn/brireyn/assets/96150916/4804be99-dee2-4c47-9378-959763fd52c4)

<h3>Step 2</h3>
Create an administrator account, do not use any default username or passwords to make it a challenge for the attackers. (A few came close to guessing my username but none succeeded to login).
Select inbound port rules for RDP (Remote Desktop protocol on port 3389) to RDP into the VM. 

![create_admin_account](https://github.com/brireyn/brireyn/assets/96150916/f753a90b-893e-46d3-9f1c-fc5c88fd780e)

<h3>Step 3</h3>

Create a Firewall with rules (NIC Avanced) 
![honeypot_firewall](https://github.com/brireyn/brireyn/assets/96150916/37813cfa-fbb3-40aa-9c80-1babd602c7f7)


Create a new NSG (Network Security Group) that allows everything into the VM. (Remove the default rules first that are blocking traffic).
![allow_any_traffic](https://github.com/brireyn/brireyn/assets/96150916/b015da59-29da-4c0f-a00c-0ab27b32b657)

This makes the VM very discoverable quickly, for ICMP pings and SIEM scans.

<h3>Step 4</h3>
Create a log analytics group in the Azure Log Analytics Workspaces. 

![create_log_analytics_group](https://github.com/brireyn/brireyn/assets/96150916/ac053d0a-05d7-40f5-9a65-b273910fddf0)

You must also enable Microsoft Defender for the use of Security Center to enable the ability to gather logs from the VM in the Security Center. 

<h3>Step 5</h3>

In Microsoft Defender for Cloud - enable ALL Events for all Windows security and Applocker events.
![Turn_off_MSDefender](https://github.com/brireyn/brireyn/assets/96150916/051f63d7-b459-42cd-9b44-81349bc62d9b)

![step5](https://github.com/brireyn/brireyn/assets/96150916/4ecfebbb-3f51-40cc-b177-85063b05f5c1)

<h3>Step 6</h3>
 Go to the Log Analytics Workspace where the VM is connected: then connect to the VM. 

![step6](https://github.com/brireyn/brireyn/assets/96150916/c3f3213b-35ad-4589-86cc-a417ace693ca)

![create_log_analytics_group](https://github.com/brireyn/brireyn/assets/96150916/cacc3c5d-e57a-40ad-8aff-8ab521b60622)


![Connect_to_vm](https://github.com/brireyn/brireyn/assets/96150916/f90df7c2-5bc1-446e-a871-c957a55db69c)

<h3>Step 7</h3>
Go to Sentinel (SIEM) used to visualize the attack data. Add Sentinal workspaces to the VM. (Associate the VM group to the SIEM workspaces).
Get the public IP of the VM and go to Remote desktop connection from  your computer's Windows menu and choose- other accounts (use Azure credentials to login to the VM). 

![public IP ](https://github.com/brireyn/brireyn/assets/96150916/96ab8e8e-f3e1-43c4-8899-7c37cbe31102)

![rdp_login](https://github.com/brireyn/brireyn/assets/96150916/be073c2d-827e-4c2f-9ac1-8e0b756e7dc5)

![rdp](https://github.com/brireyn/brireyn/assets/96150916/e4525980-6764-4168-a792-330931af5f86)
(Will get a warning, still proceed to login)
Go back to Remote Desktop Connection on your computer and create some failed log in attempts. Then log back into the VM correctly.

<h3>Step 8</h3>
After logging into the VM with the public ip , go to Event viewer in Windows and see the security logs of all the failed login attempts and the user names. 
(A fake user log text file script was also added to help 'train' the data ingestion for the PowerShell script.)

<h3>Step 9</h3>
Turn off Windows Defender Firewall setting inside of the VM, this allows ping ICMP packets to be reachable to the VM from outbound traffic and to make it discoverable to attackers trying to log in. 

![Turn_off_MSDefender](https://github.com/brireyn/brireyn/assets/96150916/fa732a07-2718-4bba-8bd9-e804067198e2)


<h3>Step 10</h3>


<h3>Step 11</h3>







<p align="center">
