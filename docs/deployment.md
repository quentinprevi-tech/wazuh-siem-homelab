# Deployment

This document describes the deployment process used for the Wazuh SIEM homelab.

## Wazuh Server

Wazuh was deployed on a dedicated virtual machine.

| Setting | Value |
|---|---|
| VM ID | 350 |
| Hostname | wazuh-siem01 |
| OS | Ubuntu Server |
| IP address | 10.10.20.50 |
| Gateway | 10.10.20.1 |
| DNS | 10.10.20.10 |
| CPU | 4 cores |
| RAM | 8 GB |
| Disk | 100 GB |
| Network | SERVERS / vmbr20 |

The Wazuh all-in-one installation was used.

Installed components:

- Wazuh manager
- Wazuh indexer
- Wazuh dashboard
- Filebeat integration

Wazuh version:

    4.14.5

## Network Placement

The Wazuh server was placed in the SERVERS network.

This keeps the SIEM close to infrastructure systems such as Active Directory and Cloud Sync while still allowing controlled access from LAN and DMZ systems through OPNsense firewall rules.

## Dashboard Access

The Wazuh dashboard is accessed from the Windows 11 lab client using HTTPS.

Dashboard URL:

    https://10.10.20.50

Access is restricted to the lab network.

## Agent Deployment

Agents were installed on:

- SRV-AD01
- SRV-SYNC01
- win11-client-lab
- web01

Windows agents were installed using the Wazuh MSI package.

Linux agent was installed using the Wazuh APT repository.

## Windows Agent Installation

Windows agents were installed with the Wazuh manager address set to:

    10.10.20.50

Example agent names:

- SRV-AD01
- SRV-SYNC01
- win11-client-lab

After installation, the Windows service was started and validated.

Service name:

    wazuhsvc

## Linux Agent Installation

The Linux agent was installed on web01.

Manager address:

    10.10.20.50

The agent service was enabled and started with systemd.

Service name:

    wazuh-agent

## Validation

Deployment was considered successful when:

- Wazuh dashboard was reachable
- All agents appeared as active
- Windows events were collected from SRV-AD01
- Nginx web events were collected from web01
- Firewall rules allowed only the required Wazuh communication
