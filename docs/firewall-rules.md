# Firewall Rules

This document summarizes the firewall rules required for the Wazuh SIEM homelab.

OPNsense is used as the firewall/router between the LAN, SERVERS, and DMZ networks.

## Network Zones

| Zone | Subnet | Purpose |
|---|---|---|
| LAN | 10.10.10.0/24 | Windows client network |
| SERVERS | 10.10.20.0/24 | Infrastructure servers and Wazuh |
| DMZ | 10.10.30.0/24 | Debian/Nginx web server |

## Wazuh Server

| Hostname | IP address | Network |
|---|---|---|
| wazuh-siem01 | 10.10.20.50 | SERVERS |

## Required Wazuh Ports

| Port | Protocol | Purpose |
|---|---|---|
| 1514 | TCP | Wazuh agent event forwarding |
| 1515 | TCP | Wazuh agent enrollment/authentication |
| 443 | TCP | Wazuh dashboard access |

## Agent Rules

The monitored systems must be able to reach the Wazuh server on TCP ports 1514 and 1515.

| Source | Destination | Ports | Purpose |
|---|---|---|---|
| SRV-AD01 / 10.10.20.10 | wazuh-siem01 / 10.10.20.50 | TCP 1514-1515 | Domain Controller agent communication |
| SRV-SYNC01 / 10.10.20.20 | wazuh-siem01 / 10.10.20.50 | TCP 1514-1515 | Cloud Sync server agent communication |
| win11-client-lab / 10.10.10.105 | wazuh-siem01 / 10.10.20.50 | TCP 1514-1515 | Windows endpoint agent communication |
| web01 / 10.10.30.10 | wazuh-siem01 / 10.10.20.50 | TCP 1514-1515 | Linux/Nginx agent communication |

## Dashboard Access

The Wazuh dashboard is accessed from the Windows 11 lab client.

| Source | Destination | Port | Purpose |
|---|---|---|---|
| win11-client-lab / 10.10.10.105 | wazuh-siem01 / 10.10.20.50 | TCP 443 | Wazuh dashboard access |

## DMZ Web Access

The Windows 11 client was allowed to access the DMZ web server for HTTP testing.

| Source | Destination | Port | Purpose |
|---|---|---|---|
| win11-client-lab / 10.10.10.105 | web01 / 10.10.30.10 | TCP 80 | Nginx HTTP testing |

## Security Approach

The firewall approach used in this lab is based on allowing only required traffic.

The SIEM server is not placed in the DMZ.

The DMZ web server only needs outbound access to the Wazuh server for agent communication and limited infrastructure services such as DNS or web updates.

## Validation

Firewall rules were validated using:

- Wazuh agent status in the dashboard
- Test-NetConnection from Windows systems
- nc from Linux systems
- Wazuh events received from each monitored host
