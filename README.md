# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet / SOC](https://i.imgur.com/ZWxe03e.jpg)

## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

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
NSG Allowed Inbound Malicious Flows Query ![Screen Shot 2023-08-23 at 8 46 11 PM](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/af384b3f-0536-4bd0-a0f7-a6e0822f8447)
<br>
NSG Allowed Inbound Malicious Flows Map![Screen Shot 2023-08-23 at 8 46 20 PM](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/6c18a1eb-e224-4c2c-835a-61481dd29144)
<br>
Linux Syslog Auth Failures Query ![Screen Shot 2023-08-23 at 8 44 30 PM](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/f00600e6-f513-4dd8-847d-b28b9ce72685)
<br>
Linux Syslog Auth Failures Map ![Screen Shot 2023-08-23 at 8 44 43 PM](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/a6acd385-289d-4b87-b178-52621ee65752)
<br>
Windows RDP Auth Failures Query ![Screen Shot 2023-08-23 at 8 43 23 PM](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/3c11c68a-f003-42af-b2e2-57eeeda3003e)
<br>
Windows RDP Auth Failures Map ![Screen Shot 2023-08-23 at 8 43 04 PM](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/4648a566-ff58-458c-b388-1c6a45f138b3)
<br>
MS SQL Auth Failures Query ![Screen Shot 2023-08-23 at 8 47 30 PM](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/5639fd3b-50d8-46bb-8522-39fb1d26dc6f)
<br>
MS SQL Auth Failures Map ![Screen Shot 2023-08-23 at 8 47 35 PM](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/fac10c90-2d06-491d-bda2-2f9363d436c8)
<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-08-22 20:26:13
Stop Time 2023-08-23 20:26:13

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 42922
| Syslog                   | 10342
| SecurityAlert            | 4
| SecurityIncident         | 361
| AzureNetworkAnalytics_CL | 3285

## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-08-24 14:07:39
Stop Time	2023-08-25 14:07:39

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 9830
| Syslog                   | 1
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

| Metric                   | % of Change after hardened environment
| ------------------------ | ---------------------------------
| Security Events (Windows VMs)            | - 77.10 %
| Syslog (Linux VMs)                       | - 99.99 %
| SecurityAlert (Microsoft Defender for Cloud)       | - 100 %
| Security Incident (Sentinel Incidents)            | - 100 %
| NSG Inbound Malicious Flows Allowed         | - 100%

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
