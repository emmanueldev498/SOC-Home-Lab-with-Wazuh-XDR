# Building a SOC Home Lab with Wazuh 


---
# Executive Summary
This project documents the successful deployment of a Security Operations Center (SOC) home lab environment utilizing the Wazuh platform. The objective was to establish a centralized Security Information and Event Management (SIEM) and Extended Detection and Response (XDR) system to monitor disparate endpoints. By leveraging Wazuh, the lab simulates an enterprise-grade security architecture designed to collect, analyze, and remediate threats across heterogeneous endpoints (Windows and Linux). The project underscores hands-on experience in log management, vulnerability assessment, and incident response.

---
# Overview
The primary goal of this lab was to move from localized logging to centralized security orchestration. By deploying a Wazuh Manager on an Ubuntu virtual machine, the lab environment provides the following:
i. Aggregation of security events from disparate operating systems.
ii. Monitor system integrity and unauthorized file changes.
iii. Detect vulnerabilities and missing patches in real-time.
iv. Identify brute-force attacks and lateral movement within the lab.
v. Manage cross-platform security through a unified dashboard.

---
# System Architecture
The architecture follows a standard manager-agent model, optimized for a virtualized home network.

i. Wazuh Manager (The Brain): An Ubuntu Server 22.04 LTS on a virtual machine (VM) running the Wazuh indexer, server, and dashboard. It acts as the central collector and analyzer.
ii. Wazuh Agents (Endpoints): Windows 11 and Kali Linux machines.
iii. Hardware: 2 vCPUs, 4 GB RAM, 50 GB disk.
iv. Network Topology & Protocol: Bridged adapter to allow communication between the host and the VM.
v. Agents communicate with the manager via AES-encrypted traffic on ports 1514 (Events) and 1515 (Registration)

---
# Implementation Steps
# Phase I: Manager Deployment
i. OS Installation: Deployment of Ubuntu Server.
ii. Wazuh Installation: Utilizing the central installation script to deploy the indexer, dashboard, and server components.
iii. Network Discovery: Initial identification of the manager IP for agent enrollment.
iv. Credential Management: Secured administrative dashboard credentials and verified connectivity via port 44.

# Phase II: Windows Agent Enrollment
i. Deployment: Invoked via PowerShell using an MSI package.
ii. Configuration: Location: C:\Program Files (x86)\ossec-agent\ossec.conf (Edit the IP in config file to the Ubuntu manager IP). 
iii. Service Activation: Manual start of the service WazuhSvc via administrative PowerShell. 


# Phase III: Linux (Kali) Agent Enrollment
i. Configuration: Editing /var/ossec/etc/ossec.conf to align with the current manager IP.
ii. Handshake: Clearing stale client.keys to force a new cryptographic association when the manager IP changes.
iii. Persistence: Enabling the wazuh-agent service to start on boot.


# SIEM & XDR Capabilities Showcasing
The lab currently demonstrates the following high-level SOC capabilities: In this section of the documentation, we provide a set of use cases to explore different Wazuh capabilities.

---

# I. Vulnerability Detection:
The Wazuh Vulnerability Detection module performs a software audit. It identifies vulnerabilities in the operating system and installed applications on monitored endpoints.

# Vulnerability Dashboard Screenshot
[![Vulnerability Detection Dashboard](<Screenshot 2026-04-29 203314.png>)](<Screenshot 2026-04-29 203314.png>)

The host has a high vulnerability exposure with 147 critical, 591 high, and 400 medium findings, mainly on MAZZI-NEW-HOST, so remediation should be treated as urgent, especially for Firefox, Windows 11, Wireshark, VirtualBox, and Node.js packages.

---

# II. File Integrity Monitoring (FIM):
The Wazuh File Integrity Monitoring module is configured to track changes in sensitive directories. Any modification to system-level files triggers an alert in the Wazuh Dashboard.

# Configuration setting for FIM on Windows Endpoint
[![Configuration setting for FIM](<Screenshot 2026-04-24 231235.png>)](<Screenshot 2026-04-24 231235.png>)

# FIM tracking all changes made in sensitive directories (Windows endpoint)
[![FIM](<Screenshot 2026-04-24 235329.png>)](<Screenshot 2026-04-24 235329.png>)

[![FIM](<Screenshot 2026-04-24 235406.png>)](<Screenshot 2026-04-24 235406.png>)

# Configuration setting for FIM on Kali Endpoint
[![Configuration setting for FIM](<Screenshot 2026-06-24 000920.png>)](<Screenshot 2026-06-24 000920.png>)

# FIM tracking all changes made in sensitive directories (Kali endpoint)
[![FIM](<Screenshot 2026-04-26 184722.png>)](<Screenshot 2026-04-26 184722.png>)

---

# III. Security Configuration Assessment (SCA): 
The Wazuh Security Configuration Assessment (SCA) module performs scans to determine if monitored endpoints meet secure configuration and hardening policies. These scans assess the endpoint configuration using policy files. These policy files contain rules that serve as a benchmark for the configurations that exist on the monitored endpoint.

[![Security Configuration Assessment Dashboard](<Screenshot 2026-04-30 140743.png>)](<Screenshot 2026-04-30 140743.png>)

This scan helps ensure that the endpoint complies with security policies and hardening configurations. The CIS benchmark for CIS Microsoft Windows 11 Enterprise shows the results of the SCA checks (passed, failed, and not applicable) and the time of the last scan, as shown above.

---

# IV. Log Analysis:
The Wazuh log data analysis module collects and analyzes logs from various sources, such as applications, systems, network devices, and cloud workloads. This data helps in resource monitoring, threat detection, and incident response.

[![Log Analysis Dashboard](<Screenshot 2026-04-30 145440.png>)](<Screenshot 2026-04-30 145440.png>)

Integrated system event logs (Both agents event logs ie auth logs, system logs, firewall logs,... ) into the Wazuh dashboard for behavioral analysis.

---

# V. Regulatory Compliance:
Wazuh helps implement compliance requirements for regulatory compliance support and visibility. This is done by providing automation, improved security controls, log analysis, and incident response. The default Wazuh ruleset supports the PCI DSS, HIPAA, NIST 800–53, TSC, and GDPR frameworks and standards. Wazuh rules and decoders are used to detect attacks, system errors, security misconfigurations, and policy violations.

# NIST 800–53 Regulatory Compliance Dashboard (Requirements)
[![](<Screenshot 2026-04-30 162622.png>)](<Screenshot 2026-04-30 162622.png>)

# NIST 800–53 Regulatory Compliance Dashboard (Events)
[![](<Screenshot 2026-04-30 162759.png>)](<Screenshot 2026-04-30 162759.png>)

---

# VI. Threat Hunting/Detection and Active Response:
It describes how Wazuh can be configured for threat prevention, detection, and response. Each use case represents a real-world scenario that users can deploy using specific configurations.

# A. Network IDS integration:
Wazuh integrates with a network-based intrusion detection system (NIDS) to enhance threat detection by monitoring and analyzing network traffic.
In this use case, we demonstrate how to integrate Suricata with Wazuh. Suricata can provide additional insights into your network's security with its network traffic inspection capabilities.

# Suricata configuration file
[![](<Screenshot 2026-04-29 130650.png>)](<Screenshot 2026-04-29 130650.png>)

[![](<Screenshot 2026-04-29 130608.png>)](<Screenshot 2026-04-29 130608.png>)
curl http://testmyids.com sends an HTTP request to the domain testmyids.com. This domain is specifically designed to trigger Intrusion Detection System alerts. The response contains known attack signatures. Security analysts use it to verify Suricata or other IDS software is detecting threats correctly.

# Wazuh's threat hunting module detecting Suricata alerts
[![](<Screenshot 2026-04-29 130511.png>)](<Screenshot 2026-04-29 130511.png>)

---

# B. Detecting a brute-force attack: 
Brute-forcing is a common attack vector that threat actors use to gain unauthorized access to endpoints and services. Services like SSH on Linux endpoints and RDP on Windows endpoints are usually prone to brute-force attacks. Wazuh identifies brute-force attacks by correlating multiple authentication failure events.

# Detecting a brute-force attack on a Kali endpoint
[![](<Screenshot 2026-05-01 184238.png>)](<Screenshot 2026-05-01 184238.png>)

# Detecting a brute-force attack on a Windows endpoint
[![](<Screenshot 2026-05-01 194015.png>)](<Screenshot 2026-05-01 194015.png>)

---

# C. Detecting unauthorized processes:
A process is a running instance of a program or system command. The Wazuh command capability detects unauthorized processes on the endpoint being monitored.

[![](<Screenshot 2026-05-04 231340.png>)](<Screenshot 2026-05-04 231340.png>)
The Wazuh process_monitor module identified that the netcat (nc) utility is currently active and listening for incoming network connections.

---

# D. Detecting an SQL injection attack:
SQL injection is an attack in which a threat actor inserts malicious code into strings transmitted to a database server for parsing and execution. When the attack is successful, it gives the attacker unauthorized access to confidential information contained in the database.

# Detecting a SQL injection attempts
[![](<Screenshot 2026-05-05 145534.png>)](<Screenshot 2026-05-05 145534.png>)

---

Wazuh also helps increase the security of some of the most comprehensive and broadly adopted cloud platforms such as AWS, Microsoft Azure, or GCP. For more information, visit https://documentation.wazuh.com/current/index.html

# Troubleshooting challenges:
• Debugged Wazuh agent ↔ manager communication issues
• Fixed XML rule errors causing service failures
• Resolved active response execution issues across endpoints
• Investigated API errors and log inconsistencies

---

# Conclusion: The Strategic Value of Wazuh in SOC Operations
Wazuh represents a pivotal advancement in cybersecurity by unifying Security Information and Event Management (SIEM) and Extended Detection and Response (XDR) into a single, open-source platform. By integrating these functions, Wazuh provides organizations with a cohesive architecture to protect endpoints, cloud workloads, and on-premise infrastructure from a centralized dashboard.

# The Wazuh Advantage in SOC Operations
In modern Security Operations Centers (SOCs), Wazuh serves as a force multiplier by addressing the core challenges of visibility, detection speed, and operational efficiency:

i. Unified Visibility: By aggregating telemetry from endpoints, cloud environments, network devices, and third-party APIs, Wazuh eliminates security blind spots, offering a "single pane of glass" view across the entire IT estate.

ii. Proactive Threat Detection: Beyond traditional log correlation, Wazuh's agent-based model enables real-time monitoring of system behavior, file integrity, and configuration status. This allows SOC teams to identify Indicators of Compromise (IoCs) and subtle deviations from baseline behavior before they escalate into full-scale breaches.

iii. Automated Incident Response: One of the most critical functions for SOC efficiency is Wazuh's active response module. By automating countermeasures-such as blocking malicious IPs or isolating compromised endpoints-Wazuh significantly reduces Mean Time to Respond (MTTR) and mitigates the impact of ongoing attacks.

iv. Compliance & Hygiene: Wazuh simplifies regulatory compliance (e.g., PCI DSS, GDPR, HIPAA, NIST 800–53) through continuous monitoring, vulnerability detection, and automated configuration assessments (SCA). This ensures that IT hygiene is maintained alongside active threat defense.

v. Intelligence-Driven Analysis: By mapping alerts directly to the MITRE ATT&CK framework and integrating diverse threat intelligence feeds, Wazuh provides SOC analysts with the necessary context to prioritize high-risk incidents, effectively reducing the noise of false positives and enabling more accurate threat hunting.

Ultimately, Wazuh empowers SOC teams to transition from a reactive posture to a proactive defense strategy. Its scalability, open-source flexibility, and ability to integrate into existing workflows make it a cornerstone for organizations seeking a robust, cost-effective, and comprehensive security operations ecosystem.

---
[You can also read the write-up on Medium](https://medium.com/@iroleemmanuel3/building-a-soc-home-lab-with-wazuh-e59aa4bcf53c)

