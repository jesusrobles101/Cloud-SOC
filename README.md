# Building a SOC + Honeynet in Azure (Live Traffic)
![image](https://github.com/user-attachments/assets/ae955caa-a136-4e2e-a371-17762b170459)

## Introduction

In this project, I constructed a mini honeynet within the Azure platform to capture and analyze security logs from various sources. These logs were then consolidated within a Log Analytics workspace. Microsoft Sentinel was utilized to leverage these logs, enabling the development of attack maps, creation of alert triggers, and generation of incidents.

The project was conducted over two distinct 24-hour phases:

- Initial Measurement Phase: Azure Sentinel monitored the metrics of an insecure environment for 24 hours.
- Post-Security Controls Phase: Security controls were implemented to fortify the virtual environment, followed by another 24-hour metric measurement period.

The metrics analyzed were:
- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Technologies, Azure Components, and Regulations Employed
- Azure Virtual Network (VNet)
- Azure Network Security Groups (NSG)
- Virtual Machines: 2 Windows VMs, 1 Linux VM
- Log Analytics Workspace with Kusto Query Language (KQL) Queries
- Azure Key Vault for Secure Secrets Management
- Azure Storage Account for Data Storage
- Microsoft Sentinel for Security Information and Event Management (SIEM)
- Microsoft Defender for Cloud to Protect Cloud Resources
- Windows Remote Desktop (RDP) for Remote Access
- Command Line Interface (CLI) for System Management
- PowerShell for Automation and Configuration Management
- [NIST SP 800-53 Revision 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final) for Security Controls
- [NIST SP 800-61 Revision 2](https://csrc.nist.gov/pubs/sp/800/61/r2/final) for Incident Handling Guidance

## Architecture Before Hardening / Security Controls
![image](https://github.com/user-attachments/assets/d26c5ee9-a8ea-474f-80cf-0b64fd77a348)

In the "BEFORE" stage of this project, I deployed a virtual environment exposed to the public Internet to attract and analyze threat actors' attack patterns. This environment included a Windows virtual machine hosting a SQL database and a Linux server, both configured with "Allow All" network security groups (NSGs) settings. Additionally, a storage account and key vault were deployed with public endpoints to further entice attackers. Microsoft Sentinel was utilized to monitor the unsecured environment, with logs aggregated by the Log Analytics workspace.


## Architecture After Hardening / Security Controls
![image](https://github.com/user-attachments/assets/fc9d55ac-439b-4d23-8740-5fcbc848b63b)


In the "AFTER" stage of this project, the environment was hardened, and security controls were implemented to comply with NIST SP 800-53 Rev4 SC-7(3) Access Points. The following hardening tactics were applied:

- Network Security Groups (NSGs): Hardened NSGs by blocking all inbound and outbound traffic, except for designated public IP addresses requiring access. This ensured that only authorized traffic from trusted sources could access the virtual machines.
- Built-in Firewalls: Configured Azure's built-in firewalls on the virtual machines to restrict unauthorized access and protect resources from malicious connections. This involved fine-tuning firewall rules based on the service and responsibilities of each VM, mitigating the attack surface.
- Private Endpoints: Replaced public endpoints with private endpoints for Azure Key Vault and Storage Containers. This limited access to the virtual network, ensuring sensitive resources were not exposed to the public Internet.

## Attack Maps Before Hardening / Security Controls
### Failed Windows RDP Logon Attempts
![windows failed rdp map](https://github.com/user-attachments/assets/5fc91792-5264-49bb-9247-87a8caae6780)
### Failed Linux Logon Attempts
![linux syslog auth failures](https://github.com/user-attachments/assets/10a27811-21a7-481d-abe6-6a1c73e5b0e3)
### External Traffic the NSGs Allowed Into the Network
![nsg malicious allowed in](https://github.com/user-attachments/assets/56cf2a88-941c-4b9d-9dfb-c35b42a4c798)
### Microsoft SQL Server Logon Attempts
![MS SQL Maps](https://github.com/user-attachments/assets/1343ff54-7bef-4b6f-97c6-7632674faab7)

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:

**Start Time:** 8/26/2024, 2:21:24.593 PM PST

**Stop Time:** 8/27/2024, 2:21:24.593 PM PST

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 42754
| Syslog                   | 3636
| SecurityIncident         | 117
| AzureNetworkAnalytics_CL | 35805

## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

After hardening the honeynet, the Network Security Groups blocked all traffic with the exception of my admin workstation. I added an NSG at the subnet level for additional network protection. I also enabled the built-in Windows firewall and implemented private endpoints for the other resources (Key Vault and Blob Storage Account).

The following table shows the metrics we measured in our environment for another 24 hours, but after we applied security controls:

**Start Time:** 8/28/2024, 2:33:27.078 PM PST

**Stop Time:**	8/29/2024, 2:33:27.078 PM PST

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 12159
| Syslog                   | 1
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Impact of Security Controls

| Metric                   | Percentage Change
| ------------------------ | -----
| SecurityEvent            | -71.56%
| Syslog                   | -99.97%
| SecurityIncident         | -100%
| AzureNetworkAnalytics_CL | -100%

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. The number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
