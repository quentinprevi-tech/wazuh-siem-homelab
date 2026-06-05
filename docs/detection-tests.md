# Detection Tests

This document describes the detection tests performed in the Wazuh SIEM homelab.

## Test 1 - Active Directory Failed Logon

### Objective

Validate that Wazuh can detect failed authentication attempts against the Active Directory domain controller.

### Source

The failed logon was generated from:

| Host | IP address |
|---|---|
| win11-client-lab | 10.10.10.105 |

### Target

| Host | IP address |
|---|---|
| SRV-AD01 | 10.10.20.10 |

### Test Command

The following command was executed from the Windows 11 client:

    net use \\10.10.20.10\IPC$ /user:HOMELAB\compte.inexistant MauvaisMotDePasse123!

### Expected Result

The authentication attempt must fail.

Expected Windows error:

    System error 1326
    The user name or password is incorrect.

### Windows Event IDs

The following Windows Security events were generated on the domain controller:

| Event ID | Meaning |
|---|---|
| 4625 | Failed logon |
| 4776 | Credential validation failure |

### Wazuh Detection

Wazuh detected the failed authentication attempt with these rules:

| Rule ID | Description | Level |
|---|---|---|
| 60122 | Logon Failure - Unknown user or bad password | 5 |
| 60104 | Windows audit failure event | 5 |

### Validation

The event was visible in Wazuh Threat Hunting with:

    agent.name:"SRV-AD01"

Screenshot:

    screenshots/srv-ad01-failed-logon-detected.png

## Test 2 - Nginx Web Request Detection

### Objective

Validate that Wazuh can collect and detect web events from the Debian/Nginx DMZ server.

### Source

The web requests were generated from:

| Host | IP address |
|---|---|
| win11-client-lab | 10.10.10.105 |

### Target

| Host | IP address |
|---|---|
| web01 | 10.10.30.10 |

### Test URLs

The following URLs were opened from the Windows 11 client:

    http://web01.homelab.local/test404
    http://web01.homelab.local/.env
    http://web01.homelab.local/wp-login.php
    http://web01.homelab.local/etc/passwd

### Nginx Log File

The custom Nginx access log used by the web01 site was:

    /var/log/nginx/web01_access.log

### Wazuh Agent Configuration

The Wazuh agent on web01 was configured to monitor the custom Nginx access log.

Relevant log source:

    /var/log/nginx/web01_access.log

### Wazuh Detection

Wazuh detected the HTTP 400/404 events with this rule:

| Rule ID | Description | Level |
|---|---|---|
| 31101 | Web server 400 error code | 5 |

### Validation

The event was visible in Wazuh Threat Hunting with:

    agent.name:"web01"

Screenshot:

    screenshots/web01-nginx-logs-wazuh.png

## Result

Both detection tests were successful.

Validated detections:

- Active Directory failed logon detection
- Windows credential validation failure detection
- Nginx HTTP 400/404 detection
- DMZ web server log collection
