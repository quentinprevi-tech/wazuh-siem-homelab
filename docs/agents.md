# Agents

This document lists the monitored endpoints and explains what each Wazuh agent collects.

## Agent Summary

| Agent | OS | IP address | Role | Status |
|---|---|---|---|---|
| web01 | Debian GNU/Linux 13 | 10.10.30.10 | DMZ Nginx web server | Active |
| SRV-AD01 | Windows Server 2022 | 10.10.20.10 | Active Directory Domain Controller / DNS | Active |
| SRV-SYNC01 | Windows Server 2022 | 10.10.20.20 | Microsoft Entra Cloud Sync server | Active |
| win11-client-lab | Windows 11 Pro | 10.10.10.105 | Domain-joined client endpoint | Active |

## SRV-AD01

SRV-AD01 is the Active Directory Domain Controller.

Collected logs include:

- Windows Security events
- Windows System events
- Windows Application events
- Authentication failures
- Credential validation events
- Domain Controller audit events

Relevant detection tests:

- Failed logon detection
- Bad password / unknown user detection
- Event ID 4625
- Event ID 4776

## SRV-SYNC01

SRV-SYNC01 is the Microsoft Entra Cloud Sync server.

Collected logs include:

- Windows System events
- Windows Application events
- Wazuh agent health events
- Cloud Sync server operating system logs

This system is monitored because it is part of the hybrid identity infrastructure.

## win11-client-lab

win11-client-lab is the Windows 11 domain-joined endpoint.

Collected logs include:

- Windows Security events
- Windows System events
- Windows Application events
- Endpoint activity logs

This client was also used to generate failed authentication attempts and web requests during validation.

## web01

web01 is a Debian/Nginx web server placed in the DMZ.

Collected logs include:

- Linux system logs
- Wazuh agent logs
- Nginx access logs
- Nginx error logs

The custom Nginx access log path is:

    /var/log/nginx/web01_access.log

The custom Nginx error log path is:

    /var/log/nginx/web01_error.log

These paths were added to the Wazuh agent configuration on web01.

## Agent Validation

Agents were considered validated when:

- The agent appeared as Active in the Wazuh dashboard
- The correct hostname was displayed
- The correct IP address was displayed
- Logs or alerts were visible in Wazuh
- Detection tests produced expected results

## Screenshots

The following screenshots support agent validation:

- wazuh-agents-active.png
- srv-ad01-failed-logon-detected.png
- web01-nginx-logs-wazuh.png
