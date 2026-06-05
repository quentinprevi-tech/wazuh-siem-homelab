# Architecture

This document describes the architecture of the Wazuh SIEM homelab.

## Overview

The lab is hosted on Proxmox VE and uses OPNsense as the firewall/router between multiple virtual network zones.

Wazuh is deployed as a dedicated all-in-one SIEM server in the SERVERS network.

Agents are installed on Windows and Linux systems to centralize security events, system logs, and web server logs.

## Network Zones

| Zone | Subnet | Purpose |
|---|---|---|
| LAN | 10.10.10.0/24 | Windows client network |
| SERVERS | 10.10.20.0/24 | Infrastructure servers and Wazuh |
| DMZ | 10.10.30.0/24 | Public-facing test web server |

## Main Components

| Component | Hostname | IP address | Network |
|---|---|---|---|
| OPNsense firewall | opnsense-lab | 10.10.10.1 / 10.10.20.1 / 10.10.30.1 | LAN / SERVERS / DMZ |
| Wazuh SIEM | wazuh-siem01 | 10.10.20.50 | SERVERS |
| Active Directory | SRV-AD01 | 10.10.20.10 | SERVERS |
| Cloud Sync server | SRV-SYNC01 | 10.10.20.20 | SERVERS |
| Windows client | win11-client-lab | 10.10.10.105 | LAN |
| Web server | web01 | 10.10.30.10 | DMZ |

## Log Flow

1. Windows and Linux agents collect local logs.
2. Agents forward events to wazuh-siem01.
3. Wazuh manager analyzes events and applies rules.
4. Wazuh indexer stores events.
5. Wazuh dashboard displays alerts and agent status.

## Wazuh Communication

| Port | Protocol | Purpose |
|---|---|---|
| 1514 | TCP | Agent event forwarding |
| 1515 | TCP | Agent enrollment and authentication |
| 443 | TCP | Wazuh dashboard access |

## Design Choices

A dedicated Wazuh VM was used instead of installing Wazuh on an existing server.

This keeps the SIEM separated from domain services and identity synchronization services.

The SIEM server is placed in the SERVERS network because it monitors infrastructure systems and should not be exposed directly from the DMZ.

## Segmentation

OPNsense controls traffic between zones.

Only required flows are allowed:

- LAN to Wazuh dashboard over HTTPS
- Agents to Wazuh over TCP 1514 and 1515
- LAN to web01 for HTTP testing
- Restricted DMZ outbound traffic for DNS, HTTP, HTTPS, and required monitoring

## Portfolio Value

This architecture demonstrates:

- Network segmentation
- Centralized security monitoring
- Windows and Linux log collection
- DMZ web server monitoring
- SIEM deployment in a realistic homelab environment
