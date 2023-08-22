# SIEM-Analysis-Lab
SIEM Analysis lab made in Microsoft Azure environment utilizing log aggregation collection to a third party API to track failed login attempts to a honeypot VM.

<h1>SIEM Analysis Lab in Azure</h1>

<h2>Description</h2>
<p>This cybersecurity lab project consists of using Microsoft Sentinel's SIEM service,Security Center, Azure Virtual Machine as the the honeypot with an open Network Security group to allow inbound access to entice attackers' activity.The PowerShell script collects the VM's Windows Event Viewer- Event Log Error code (4625) alerts that are failed login attempts. The script then automates the logs to be parsed by a third party API (ipgeolocation.io) to provide geolocation of the IP address from the failed login attempts. The API's data is then ingested into a custom table in Azure Log Analytics which ingests and parses the failed RDP log in attempts and plots to a workbook in Microsoft Sentinel as a real-time live map with coordinates of the attacker's IP address, username, country and state. The logs are filtered based on labels for analytical data and alerts based on the error code of a failed log in attempt to the VM.   /p>

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

<p align="center">
