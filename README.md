# SIEM-Analysis-Lab
SIEM Analysis lab created in Microsoft Azure environment utilizing log aggregation collection to a third party API to track failed login attempts to a honeypot VM.

<h1>SIEM Analysis Lab in Azure</h1>

<h2>Description</h2>

This cybersecurity lab project consists of using Microsoft Sentinel's SIEM service,Security Center, Azure Virtual Machine as the the honeypot with an open Network Security group to allow inbound access to entice attackers' activity.The PowerShell script collects the VM's Windows Event Viewer- Event Log Error code (4625) alerts that are failed login attempts. The PowerShell script automates the logs to be parsed by a third party API (ipgeolocation.io) to provide geolocation of the IP address from the failed login attempts; (longitutde and latitude coordinates). The API's data is then ingested into a custom table in Azure Log Analytics which ingests and parses the failed RDP log in attempts and plots to a Workbook in Microsoft Sentinel as a real-time live map with coordinates of the attacker's IP address, username, country and state. The logs are filtered based on labels for analytical data and alerts based on the error code of a failed log in attempt to the VM.   

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

*Get API key from here: https://ipgeolocation.io/ ( this is used inside of the PowerShell script) 

<h3>Step 10</h3>
I created some network traffic by using the CMD on my own computer by pinging the VM's public IP address. 

![stopped_ping](https://github.com/brireyn/brireyn/assets/96150916/db66f3b2-2233-4732-8a44-053f9c636ea8)

(Before disabling the firewall inside of the VM, pinging will fail as the host is not yet reachable to inbound network traffic.)
![before_turningoff_firewall](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/7ee6e84b-8d82-45f9-8279-73477e94bca7)

<h3>Step 11</h3>
(Inside the VM) The Event Viewer inside the VM shows audit activity under security logs. 

![sec_logs_audits](https://github.com/brireyn/brireyn/assets/96150916/4f02263c-45c5-4a83-9103-54b3cd84bf31)

Insert the PowerShell script inside of PowerShell ISE in the VM and make sure to use a custom API key from the ipgeolocation.io API (third party website) that parses the IP address from the login log data and gives the geolocation of the user back to the PowerShell script. The logs will be ingested into Log Analytics Workspaces and then will use the Microsoft SIEM to read the latitude and longitude of the geolocation information.

![replace_with_own_API_key](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/10fb730c-305b-462e-acbb-ca1a0bbfff57)

![powershell_download_script](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/087af881-f0d9-4fb8-910d-37698550ba4e)
![image](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/2de321cb-a059-4c50-a384-ac2f1bf5ff59)


<h3>Step 12</h3>
What is happening with how the powershell script works -

The script finds any failed login attempts ending in the Event Log Error code 4625 and will propagate the logs in the pink output below in powershell ISE, the script is sent to the API ipgeolocation.io and gets the data returns the ip coordinates geolocation. 
-Will use the programdata (hidden file) to train Log Analytics in Azure to parse out the log data.

![logger_run](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/aeaeec67-1246-4738-8ae4-30fdd55d2cea)

Add a text file of rdp_failed.logs to the Program data file which is hidden. 

![program_data](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/cc7763ee-6ea5-49dd-b52e-5d0229d3e33d)

An example of me failing a login attempt at the bottom of the running the PowerShell Script:


![fail_ex](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/c0a25dbe-1aec-4206-880e-a70b28e133dd)


<h3>Step 13</h3>

Go into Azure Log Analytics Workspaces.

Create the log analytics group:

![create_log_analytics_group](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/dd22d85b-8cf9-43f4-9a38-8f8ecb4fc762)

create a custom log table to direct the path to the Program Data folder inside of the VM where the failed_rdp.log text file is stored along with the PowerShell script that is running.
![create_custom_log_azure](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/f7b60679-2582-4306-b98a-5ec470624fe7)
Create a custom log
 log in tables- MMR - 
Create a custom log in analytics inside of azure and copy the failed event log to notepad from vm to PC .. Import failed event log to azure custom log. 
Copy the contents of failed_rdp log data.

![create_custom_log_2](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/dda67646-6bfd-40bf-b026-6feaa49ec60e)
Record Delimiter:
![custom_log_3](https://github.com/brireyn/brireyn/assets/96150916/c5c98340-6070-4629-be03-b5abfe3c5b82)

![create_customlog_3](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/1734d263-aee1-486b-8335-ce89581d169e)

<h3>Step 14</h3>
Run the custom query for the custom log named (FAILED_RDP_WITH_GEO_CL ) in Log Analytics - tables - failed_rdp.log
Query the custom table which shows the failed_rdp.logs from the VM using: SecurityEvent | where EventID ==4625   
4625 is the error number for failed login attempt from Windows Event Viewer.

![run_query_on_logs](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/2a249f33-d898-4c52-8ea9-3ca6c830f80a)

![ran_custom_query](https://github.com/brireyn/brireyn/assets/96150916/54a46254-ecea-4095-ab66-958aefab71a3)

![Screenshot 2023-08-21 015101](https://github.com/brireyn/brireyn/assets/96150916/58a5e4c5-7320-4ec4-bbc7-e6a435c88fc4)

![Step14](https://github.com/brireyn/brireyn/assets/96150916/deff20d6-37e5-4d05-a96c-c3e2a5a914b0)

In the Alert log query result, the RAW data is showing the username, IP address, location information, workstation and timestamp for the failed login.

![alert_log_query_result](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/d51364eb-fc13-4007-b83e-42be4cecbda1)

<h3>Step 15</h3>

Create a new workbook inside of Microsoft Sentinel for creating a query script to extract the Raw data (detailed info) from the FAILED_RDP_WITH_GEO_CL table and then Sentinel Workbook will plot the raw data into a visual map.

![workbook_ploting_map_query](https://github.com/brireyn/brireyn/assets/96150916/20d55d5c-f279-4dff-9c5a-d42dc9c47a43)
Raw data will be automatically extracted from the custom analytics Log table by Sentinel Workbook. 

So far in the Azure dashboard overview:
![honeypot_group](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/d6aee0d8-839c-4348-88f6-98788c669001)

<h3>Creating the map query</h3>:

![map_query](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/733b87ea-9096-41d3-8b01-0fe6eb4ff3d7)

![map_query_settings](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/a7e655f2-1959-4866-8693-803028ea634f)

![maP-plotting](https://github.com/brireyn/brireyn/assets/96150916/dc804bef-11b0-40cd-9a2a-0e0d91e57483)

<h3>This is the live map after 1 hour running the VM.</h3>

![RDP_Map](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/52034414-19b7-449b-9162-fc0fc746ab95)

<h3>End Results after 5 hours</h3>
I began to notice the first attack from someone in England, UK after about 2 hrs. 

![1hour_map_view](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/125e14ac-2ce5-48a5-a574-7acda47f72f1)
I received mainly default guesses since the VM was named honeypot_VM. 

![england_close](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/84002063-f1ef-4ddd-a808-a251ff73e664)


![failed_event_log_8 21](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/b7b905b7-fbdf-4d53-9370-02fee6c0b0be)


![failed_event_log_8 21-1](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/e9bf6ffd-82cb-4943-a460-b515247cf79e)


The RDP Heatmap after about 5 hours: 

![4hrs_attack_log](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/3cc099f7-3eff-4948-90ff-c28c67e20041)

This person from Saudi, Arabia came close to guessing the VM's username. But no one succeeded to log into the VM. 

![close_username_guessing](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/0698ca1c-d200-4b37-8a37-4024d6920440)
Sentinel Data:
![sentinel_data](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/e9135bda-afb6-4400-bf08-c8fa188fa424)

Security Events Insights:

![sec_events_insights](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/d6146d5b-136c-4ad4-ba6d-c214d80c046b)

![Screenshot 2023-08-21 222404](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/9488dfb5-4b8e-4505-b8f0-4b7dcac13f85)
![Screenshot 2023-08-21 222344](https://github.com/brireyn/SIEM-Analysis-Lab/assets/96150916/1d4e288a-3424-42cc-aa3e-1b30de3ee606)

Thank you for taking the time to preview this lab! The end results were interesting in seeing the attackers appeared to search for VM's that were running for more than an hour and NSG's rules fully opened. 

<p align="center">

</p>

