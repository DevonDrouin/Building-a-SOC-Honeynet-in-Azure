# Building a SOC + Honeynet in Azure (Live Traffic)
[Cloud Honeynet / SOC]![image](https://github.com/user-attachments/assets/6272db93-d3f0-4a5a-be6d-06e997713555)

## Introduction

In this project, I built a mini honeynet in Azure and ingested log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, applied some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![image](https://github.com/user-attachments/assets/8dd4fbcc-afd8-4f37-9478-ce1f79b875bb)



## Architecture After Hardening / Security Controls
![image](https://github.com/user-attachments/assets/78ad5e2f-6b61-4e67-ba78-de60e47d5b32)



The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
[NSG Allowed Inbound Malicious Flows]![nsg-malicious-allowed-in ](https://github.com/user-attachments/assets/d9cfa607-efe3-47d5-8c20-66f02c64def9)
<br>
[Linux Syslog Auth Failures]![linux-ssh-auth-fail ](https://github.com/user-attachments/assets/e626f260-170c-432c-8957-36aa39b8a9b9)
<br>
[Windows RDP/SMB Auth Failures]![windows-rdp-auth-fail ](https://github.com/user-attachments/assets/02c874ca-9c87-48fb-a1ad-502cc1729948)
<br>
[Mssql-auth-fail]![Mssql-auth-fail ](https://github.com/user-attachments/assets/02c874ca-9c87-48fb-a1ad-502cc1729948)
<br>
## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2024-12-12 23:18
Stop Time 2024-12-13 23:18

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 6611
| Syslog                   | 15903
| SecurityAlert            | 6
| SecurityIncident         | 324
| NSG Inbound Malicious Flows Allowed/ AzureNetworkAnalytics_CL | 2660

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2024-12-18 20:33
Stop Time	2024-12-19 20:33

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 506
| Syslog                   | 0
| SecurityAlert            | 0
| SecurityIncident         | 4
| NSG Inbound Malicious Flows Allowed/ AzureNetworkAnalytics_CL | 0

The following Table shows the percentage of the metrics that had changed after the security controls had been applied

| Metric                   | Change after security controls 
| ------------------------ | -----
| SecurityEvent            | -92.35%
| Syslog                   | -100.00%
| SecurityAlert            | -100.00%
| SecurityIncident         | -98.77%
| NSG Inbound Malicious Flows Allowed/ AzureNetworkAnalytics_CL | -100.00%

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness. The only Security events were falsely flagged as they were "NT AUTHORITY\SYSTEM" that had not been excluded in the alert rules. 

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
