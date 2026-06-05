# Troubleshooting

This document summarizes the main issues encountered during the Wazuh SIEM homelab project and how they were resolved.

## Proxmox Repository Mismatch

### Issue

The Proxmox host was running Debian 13 / Proxmox VE 9, but some APT repositories were still pointing to Debian 12 bookworm.

This created dependency issues when trying to install diagnostic tools.

### Symptoms

APT tried to resolve packages from the wrong Debian version.

Python versions did not match the expected repository state.

### Fix

The Debian, Proxmox, and Tailscale repositories were corrected to use trixie.

After correcting the repositories, the host was upgraded cleanly to Proxmox VE 9.2.

### Result

APT became consistent again and all packages were up to date.

## Disk I/O Wait

### Issue

The lab felt slow after adding Wazuh and multiple Windows/Linux agents.

### Symptoms

Proxmox showed high I/O wait.

Initial value observed:

    wa around 21%

### Fix

The Proxmox host repositories were corrected and the host was upgraded.

Old intermediate snapshots were removed.

The LVM thinpool usage was reduced from around 50% to around 37%.

### Result

Disk I/O wait returned to 0%.

## QEMU Guest Agent on Windows VMs

### Issue

Some Windows VMs showed the QEMU Guest Agent service as running inside Windows, but Proxmox could not communicate with the agent.

### Affected VMs

- SRV-AD01
- SRV-SYNC01

### Symptoms

Proxmox returned:

    QEMU guest agent is not running

### Fix

The full VirtIO Guest Tools package was installed from the VirtIO ISO.

After reinstalling the guest tools and rebooting the VMs, Proxmox was able to query the guest agent again.

### Result

Proxmox successfully returned IP address information for the Windows VMs.

## Wazuh Nginx Log Path

### Issue

Wazuh did not show the expected Nginx access logs for web01.

### Cause

Nginx was not writing to the default file:

    /var/log/nginx/access.log

The site-specific Nginx configuration was writing to:

    /var/log/nginx/web01_access.log

### Fix

The Wazuh agent configuration on web01 was adjusted to monitor the custom Nginx log file.

Correct log source:

    /var/log/nginx/web01_access.log

### Result

Wazuh detected web events from web01 and generated alerts for HTTP 400/404 responses.

## Windows Failed Logon Detection

### Issue

Initial failed logon tests did not appear immediately in Wazuh.

### Cause

Older Windows Security events existed before the Wazuh agent was installed, and audit policy settings needed to be validated.

### Fix

A new failed authentication attempt was generated after installing the agent.

Domain Controller audit settings were configured to include success and failure auditing for:

- Logon
- Credential validation
- Kerberos authentication service

A dedicated GPO was created for Domain Controller audit settings.

### Result

Wazuh detected the failed authentication attempt from SRV-AD01.

Relevant Windows event IDs:

- 4625
- 4776

Relevant Wazuh rules:

- 60122
- 60104

## Snapshot Warnings

### Issue

Proxmox displayed warnings about thin provisioning and thin pool usage when creating snapshots.

### Cause

The sum of thin-provisioned VM disks and snapshots was higher than the physical storage size.

This is expected with thin provisioning, but it must be monitored.

### Fix

Old intermediate snapshots were removed.

### Result

Thinpool usage was reduced and the storage state became healthier.

## Lessons Learned

Main troubleshooting takeaways:

- Always verify APT repositories after a major Proxmox upgrade.
- Do not force package downgrades when the repository state is unclear.
- Wazuh can be resource-intensive after deployment and when collecting from multiple agents.
- SIEM logs depend on the real application log path, not only default paths.
- Windows audit policies must be validated before testing SIEM detections.
- Snapshots are useful, but too many snapshots increase storage risk.
