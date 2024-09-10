# Azure-SOC + Honeynet (Live Attacks)
![Cloud Honeynet + SOC](https://github.com/user-attachments/assets/6d3313ea-7501-41f4-b0cf-4e806d702553)





## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into Azure's Log Analytics workspace, which Microsoft Sentinel then uses to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, then applied some security controls to harden the environment, measured metrics for another 24 hours, and then showed the results below. The metrics I am showing are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

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
  - **Built-in Firewalls:** Azure's built-in firewalls were configured on the virtual machines to restrict unauthorized access and defend against malicious connections. Custom firewall rules were applied according to the role and service of each VM, reducing the attack surface and enhancing overall protection.
  - **Private Endpoints:** To strengthen the security around the other Azure resources, private endpoints replaced the public ones. This safeguarded access to sensitive resources, such as storage accounts and databases, was allowed only inside of the virtual network, and was not exposed to the public internet as it previously was. Resulting in protection from unauthorized access and potential attacks.

## Attack Maps Before Hardening / Security Controls

- This attack map displays the aftermath of leaving Network Security Group (NSG) open to the public internet. This map demonstrates the importance of implementing proper security methods. For example, as explained above, restricting the NSG rules to only allow access from authorized users. 
![(before) nsg-malicious-allowed-in #2](https://github.com/user-attachments/assets/52645569-ae02-4e52-866f-d20f09ceb9dc)<br>



- This attack map highlights the significant number of syslog authentication failures experienced by the Linux VM, indicating unauthorized access attempts from the internet. It clearly emphasizes the critical need to secure not only Linux servers but all resources. Additionally, it underscores the importance of robust authentication measures and the continuous monitoring of system logs for potential intrusion attempts.
![(before) linux-ssh-auth-fail #2](https://github.com/user-attachments/assets/b64585c1-07c5-4097-95c9-d5d85e2c17c2)<br>



- This map highlights all the failed attempts involving Remote Desktop Protocol (RDP) and Server Message Block (SMB), revealing attackers' efforts to exploit these protocols. It demonstrates the critical need to secure both remote access and file-sharing services to defend against unauthorized access and potential cyber threats.
![(before) windows-rdp-auth-fail #2](https://github.com/user-attachments/assets/7443e4bf-0a25-4bd6-bf3e-d4c233a0ed30)<br>



## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-03-15 17:04:29
Stop Time 2023-03-16 17:04:29

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 19470
| Syslog                   | 3028
| SecurityAlert            | 10
| SecurityIncident         | 348
| AzureNetworkAnalytics_CL | 843

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-03-18 15:37
Stop Time	2023-03-19 15:37

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 8778
| Syslog                   | 25
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
