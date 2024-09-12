# Azure Honeynet and SOC with Real-World Cyber Attacks
![Cloud Honeynet + SOC](https://github.com/user-attachments/assets/6d3313ea-7501-41f4-b0cf-4e806d702553)





## Introduction

In this project, a small honeynet was built within the Azure cloud environment. The aim was to gather and analyze logs from multiple sources, which were then collected in a Log Analytics workspace. Microsoft Sentinel was then utilized to interpret these logs, create attack maps, set alert triggers through watchlists that were created, and then generate incidents based on the logs collected. Metrics were recorded from the unsecured environment for 24 hours. Afterward, security controls were applied to strengthen the virtual environment. Finally, another 24-hour period was used to gather new metrics, and the results of both phases are explained below. The metrics that were analyzed include:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into the honeynet)

## Technologies, Regulations, and Azure Components Employed:
- Azure Virtual Network (VNet)
- Azure Network Security Group (NSG)
- Virtual Machines (2x Windows, 1x Linux)
- Log Analytics Workspace with Kusto Query Language (KQL) Queries
- Azure Key Vault for Secure Secrets Management
- Azure Storage Account for Data Storage
- Microsoft Sentinel for Security Information and Event Management (SIEM)
- Microsoft Defender for Cloud to Protect Cloud Resources
- Windows Remote Desktop for Remote Access
- Command Line Interface (CLI) for System Management
- PowerShell for Automation and Configuration Management
- [NIST SP 800-53 Revision 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final) for Security Controls
- [NIST SP 800-61 Revision 2](https://www.nist.gov/privacy-framework/nist-sp-800-61) for Incident Handling Guidance

## Methodology
- **Creating the Honeynet:** First I [deployed A Linux Virtual Machine and a Windows 10 Virtual Machine in Azure](https://github.com/Jlagerstrom12/Creating-Honeypot-In-Azure/blob/main/README.md), and then I configured them so they would be insecure.
- **Monitoring and Analysis:** Second I configured Azure to ingest various logs into a Log Analytics Workspace I created. I also set up Microsoft Sentinel to build attack maps showing the geolocation data of where attacks were coming from. Sentinel was also configured to trigger alerts and open incidents based on the data that had been collected.
- **Measuring Security Metrics:** I let the environment run for at least 24 hours, and recorded specific security metrics. These metrics include but are not limited to Brute force attempts on the Windows and Linux VMs and Privilege Escalation. Collecting these logs gave me baseline metrics about the vulnerable systems being open to the internet, which I can compare to the logs after hardening the system.
-  **Incident Response and Remediation:** After Identifying the vulnerabilities in the systems, I hardened the system and implemented some of NIST SP 800-53 R5 SC-7(3) "Access Points".
-  **Post-remediation analysis:** After Hardening the environment, I observed the environment for another 24 hours to re-measure the metrics again so I could compare the two sets.

## Architecture Before Hardening and Implementing Security Controls
![Public Internet Open](https://github.com/user-attachments/assets/a30b1986-5dd4-474b-a99f-e615ac3e48f2)

### Before Hardening Measures and Security Controls: 

- In the "BEFORE" phase of the project, all resources were intentionally deployed with public internet exposure. This deliberately insecure configuration aimed to attract potential cyber attackers and monitor their techniques. The Virtual Machines had their Network Security Groups (NSGs) and internal firewalls fully open, allowing unrestricted access from any source. Furthermore, other resources, including storage accounts and databases, were configured with public endpoints. Without employing Private Endpoints to enhance security, these other resources being deployed were intentionally sweetening the honeynet.




## Architecture After Hardening and Implementing Security Controls
![Public Internet Hardened](https://github.com/user-attachments/assets/8f75036c-3b04-489d-93b9-3e0a5fee6f42)

In the "AFTER" phase, the environment was strengthened, and security measures were put in place to comply with NIST 800-53 Rev5 SC-7 (3) "Access Points." The following actions were taken:
  - **Network Security Groups (NSGs):** All traffic was blocked except from a specific public IP address that needed access to the virtual machines. This ensured that only an authorized source could establish connections, securing network traffic.
  - **Built-in Firewalls:** Azure’s built-in firewalls were set up on the virtual machines to block unauthorized access and protect against malicious traffic. Custom firewall rules were tailored to the specific function and role of each VM, reducing the size of the attack surface and improving security overall.
  - **Private Endpoints:** To improve the security of Azure Key Vault and Storage Containers, Public Endpoints were swapped for Private Endpoints. This change restricted access to these critical resources to the virtual network, preventing further exposure to the public internet. Making these accessible only within the virtual network instead of the entire internet. 
## Attack Maps Before Hardening / Security Controls<br/>
<br/>
<br/>

- This attack map displays the aftermath of leaving Network Security Group (NSG) open to the public internet. This map demonstrates the importance of implementing proper security methods. For example, as explained above, restricting the NSG rules to only allow access from authorized users. 
![(before) nsg-malicious-allowed-in #2](https://github.com/user-attachments/assets/52645569-ae02-4e52-866f-d20f09ceb9dc)<br>
<br/>
<br/>

- This attack map highlights the significant number of syslog authentication failures experienced by the Linux VM, indicating unauthorized access attempts from the internet. It clearly emphasizes the critical need to secure not only Linux servers but all resources. Additionally, it underscores the importance of robust authentication measures and the continuous monitoring of system logs for potential intrusion attempts.
![(before) linux-ssh-auth-fail #2](https://github.com/user-attachments/assets/b64585c1-07c5-4097-95c9-d5d85e2c17c2)<br>
<br/>
<br/>

- This map highlights all the failed attempts involving Remote Desktop Protocol (RDP) and Server Message Block (SMB), revealing attackers' efforts to exploit these protocols. It demonstrates the critical need to secure both remote access and file-sharing services to defend against unauthorized access and potential cyber threats.
![(before) windows-rdp-auth-fail #2](https://github.com/user-attachments/assets/7443e4bf-0a25-4bd6-bf3e-d4c233a0ed30)<br>
<br/>
<br/>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:<br/>
Start Time 2024-07-30 17:43<br/>
Stop Time 2024-07-31 17:43

| Metric                                                         | Count
| -------------------------------------------------------------- | -----
| SecurityEvent (Windows VM)                                     | 32078
| Syslog (Linux VM)                                              | 5562
| SecurityAlert (Microsoft Defender for Cloud)                   | 36
| SecurityIncident (Sentinel Incidents)                          | 388
| AzureNetworkAnalytics_CL (NSG Inbound Malicious Flows Allowed) | 2780

## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we applied security controls:<br/>
Start Time 2024-08-02 19:10<br/>
Stop Time	2024-08-03 19:10

| Metric                                                         | Count
| -------------------------------------------------------------- | -----
| SecurityEvent (Windows VM)                                     | 0
| Syslog (Linux VM)                                              | 1
| SecurityAlert (Microsoft Defender for Cloud)                   | 0
| SecurityIncident  (Sentinel Incidents)                         | 0
| AzureNetworkAnalytics_CL (NSG Inbound Malicious Flows Allowed) | 0

## Conclusion

In this project, a mini honeynet was set up in Microsoft Azure, with log sources connected to a Log Analytics workspace. Microsoft Sentinel was used to generate alerts and create incidents based on the logs ingested from the watchlists. Metrics were collected in the unsecured environment before any security controls were added, and then again after implementing security measures. Notably, the number of security events and incidents dropped significantly after applying the security controls, demonstrating their effectiveness.

It's important to mention that if regular users actively used the network's resources, it's likely that more security events and alerts would have been triggered within the 24-hour period following the hardening tactics implemented.
