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

I. Vulnerability Detection: The Wazuh Vulnerability Detection module performs a software audit. It identifies vulnerabilities in the operating system and installed applications on monitored endpoints.

