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
- Microsoft Defender for Cloud
- Windows Remote Desktop
- Command Line Interface
- Powershell
- NIST SP 800-53 r4
- NIST SP 800-61 r2

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

## Simulated Attacks

Before hardened the enviroment, I took the opportunity to simulate specific attacks via powershell scripts or by manually triggering events. The results were observed in Log Analytics Workspace and Sentinel Incident Creation

  - Linux Brute Force Attempt
  - AAD Brute Force Success
  - Windows Brute Force Success
  - Malware Detection (EICAR Test File)
  - Privelege Escalation

![image](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/1b026321-c8d8-4f2a-8cca-b5eeeebb408c)
![image](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/38fe25ce-880e-4178-a7ef-270f44e42e28)
![image](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/c4b6b87a-28cb-460f-86b4-b1e415e68d32)



## Utilizing NIST 800.61r2 Computer Incident Handling Guide

For each simulated attack I then practiced incident response following NIST 800-61 r2.

![image](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/ae3f350a-9ef3-415c-87dd-99b38f65486c)

### Preparation 

The Azure lab was set up to ingest all of the logs in the the Log Anayltics Workspace, Sentinel and Defender for Cloud were configured, and alert rules were put into place.

### Detection & Analysis

• Malware is detected on a workstation with the potential to compromise the confidentiality, intergrity, or availibility of the system and data.
• Assigned alert to an owner (myself) and set severity to HIGH and status to ACTIVE
• Identified the primary user account of the system and its affected systems
• A full scan of the system was conducted using up-to-date antivirus software to identify the Malware.
• Verified the authenticity of the alert as either a true or false positive.

### Containment, Eradication, & Recovery 

• The infected system(s) were quarentined from the enviroment. During the lab this was done by shutting off the VM, similar to an on-prem computer being unplugged
• Depending on the organizational policies, the affected systems could be restored to clean state such as system image or clean installation of the operating system and applications. An up-to-date anti-virus solution can also be used to clean the affected system.

### Post Incident Activity

• In this simulated case, an employee had downloaded a game that contained Malware.
• All informationed was gathered and analzyed to determine the root cause, extent of damage, and effectiveness of the response.
• Report disseminated to all stakeholders
• Corrective actions are implemented to remediate the root cause.
• Lessons-learned review of the incident was conducted. 

![image](https://github.com/jamesgmoore/Azure-SOC-HoneyNet/assets/143222991/36f9df4d-3213-4a8b-a5e3-e33b08e24e10)




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

## Hardening Steps

For the AFTER stage, I implemented a series of hardening measures and security controls to improve the enviroment's overall security posture. These improvesments included:

   #### Network Security Groups (NSGs): 
   
   I hardened the NSGs by blocking all inbound and outbound traffic, with the sole exception of my own public IP Address. This ensured that only authorized traffic from a trusted source was allowed to access my virtual machines.
  
   #### Built In Firewalls: 
   
  I configured the built-in firewalls on the virtual machines torestrict access and protect the resources from unauthorized connections. This step involved fine-tuning the firewall rules based on the specific requirements of each VM, thereby minimizing the potential attack surface.

   #### Private Endpoints: 
   
   To enhance the security of other Azure resources, I replaced the public endpoints with private endpoints. This ensured that access to sensitive resources such as storage accounts and databases, were limited to the virtual network and not exposed to the public internet. As a result, These resources were protected from unauthorized access and potential attacks. 


## Overall Improvement

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
