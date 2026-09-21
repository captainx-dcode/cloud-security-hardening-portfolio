# 01 - Baseline Lynis Audit

**Date:** 2026-09-21
**Command:** `sudo lynis audit system`
**Tool version:** Lynis 3.0.7
**Target:** Ubuntu 22.04.4 LTS container running Apache 2.4.52

This audit was run **before any changes**. It is the "before" measurement that the post-hardening audit will be compared against.

---

## 1. Summary of results

| Metric | Baseline |
|---|---|
| **Hardening index** | **56 / 100** |
| Tests performed | 245 |
| Warnings | 4 |
| Suggestions | 48 |
| Firewall component | Detected |
| Malware scanner component | Not found |

Full scan output is saved in [`evidence/1-scanning_output.txt`](../evidence/1-scanning_output.txt). Lynis also writes `/var/log/lynis.log` and `/var/log/lynis-report.dat` inside the container.

## 2. Warnings (highest severity)

| ID | Finding | Interpretation |
|---|---|---|
| AUTH-9208 | Multiple accounts share the same UID | Real finding. Shared UIDs break accountability because two accounts are indistinguishable to the OS. |
| PKGS-7392 | Vulnerable packages found | Real finding. Patching required. |
| FIRE-4512 | iptables modules loaded but no rules active | Partly environmental. The container lacked permission to read the ruleset. In the cloud this is handled by security groups and network ACLs. |
| LOGG-2138 | klogd not running | Related to the missing logging daemon (see LOGG-2130). |

## 3. Notable observations from the scan output

- **Sudoers:** `/etc/sudoers.d/` contains files for `Cypher`, `Trinity`, and `Neo`. The directory and the `Trinity` file were flagged with permission warnings.
- **Insecure services:** `xinetd` is installed and active, with **telnet** configured. Telnet transmits credentials in cleartext.
- **Apache:** 6 virtual hosts, 119 loadable modules. `mod_evasive` and `mod_reqtimeout` are present. **ModSecurity (WAF) is not installed.**
- **Logging and accounting:** no syslog daemon, no `auditd`, no process accounting.
- **Home directories:** permission and ownership warnings.
- **Umask:** weak default in `/etc/profile` and `/etc/login.defs`.
- **Security frameworks:** no AppArmor, SELinux, or other MAC framework detected.
- **Malware and integrity tooling:** none installed.

## 4. Container caveat

Lynis is designed for full hosts. Several results come from the container environment rather than genuine misconfiguration:

- systemd is not PID 1 ("Failed to connect to bus"), so service and boot checks return empty or unknown.
- No bootloader, kernel image, or `/boot/vmlinuz` (KRNL-5788, PKGS-7410).
- Kernel `sysctl` values are shared with the host, so many cannot be changed from inside the container.
- The firewall ruleset could not be read without `NET_ADMIN`.
- Physical hardware suggestions (USB, FireWire) do not apply to a cloud VM.

These are recorded but **not counted as remediation targets**. They are useful for the cloud discussion: in IaaS the customer manages the guest OS, while the provider handles hardware and hypervisor concerns.

## 5. Triage of findings

CIS Controls v8 safeguards below are Implementation Group 1 unless marked otherwise. Ubuntu 22.04 and Apache benchmark section numbers will be added per fix in the remediation log.

### Priority 1: remediate first

| Lynis ID | Finding | CIS v8 mapping |
|---|---|---|
| AUTH-9208 | Duplicate UIDs | 5.1 Establish and maintain an inventory of accounts |
| INSE-8100 / 8116 / 8322 | xinetd and telnet enabled | 4.1 Secure configuration process; 4.8 Disable unnecessary services (IG2) |
| Sudoers permissions | `/etc/sudoers.d` and `Trinity` file | 5.4 Restrict administrator privileges; 3.3 Configure data access control lists |
| PKGS-7392 | Vulnerable packages | 7.3 Automated OS patch management; 7.4 Automated application patch management |
| HTTP-6643 | No ModSecurity WAF | 4.1 plus Apache benchmark |

### Priority 2: remediate next

| Lynis ID | Finding | CIS v8 mapping |
|---|---|---|
| LOGG-2130 / 2138 | No syslog daemon or klogd | 8.1 / 8.2 Audit log management and collection |
| ACCT-9628 | auditd not enabled | 8.2 Collect audit logs |
| AUTH-9328 | Weak umask | 3.3 Data access control; 4.1 |
| HOME-9304 / 9306 | Home directory permissions and ownership | 3.3 Data access control lists |
| FILE-7524 | Restrictive file permissions needed | 3.3 Data access control lists |
| AUTH-9230 / 9262 / 9282 / 9286 / 9288 | Password policy, aging, unused accounts | 5.2 Unique passwords; 5.3 Disable dormant accounts; 4.1 |
| BANN-7126 / 7130 | No legal banners | 4.1 Secure configuration |
| HRDN-7230 | No malware scanner | 10.1 Deploy and maintain anti-malware software |
| DEB-0810 / 0811 / 0831 / 0880 | apt-listbugs, apt-listchanges, needrestart, fail2ban | 7.3 Patch management; 4.1 |
| NETW-3200 | Unused protocols (dccp, sctp, rds, tipc) | 4.1 Secure configuration |
| PKGS-7370 / 7394 | debsums, apt-show-versions | 7.3 Patch management |

### Defer or document only

| Lynis ID | Reason |
|---|---|
| BOOT-5180 / 5264, KRNL-5788, PKGS-7410 | Container has no systemd, bootloader, or kernel packages |
| KRNL-6000 (sysctl values) | Host-controlled kernel; revisit for network sysctls only |
| FIRE-4512 | Needs `NET_ADMIN`; cloud equivalent is security groups |
| FILE-6310 | Separate partitions; in cloud, use separate volumes |
| USB-1000, STRG-1846 | Physical hardware, not applicable to cloud |
| NAME-4028 | DNS domain name; environment-specific |
| FINT-4350, TOOL-5002 | File integrity and automation tooling; beyond IG1, revisit in the cloud recommendation |
| MAIL-8820 | Review whether a mail service is actually needed |

## 6. Remediation plan

1. Identify and resolve duplicate UIDs; review all accounts.
2. Remove telnet and xinetd.
3. Fix sudoers permissions and review sudo rights.
4. Apply package updates.
5. Install and configure logging (rsyslog, auditd).
6. Tighten umask, home directory permissions, and file permissions.
7. Apply password policy settings in `/etc/login.defs`.
8. Install ModSecurity and review Apache configuration against the CIS Apache benchmark.
9. Add banners, install malware scanner, disable unused protocols.
10. Re-run Lynis and compare.

## 7. Evidence

| File | Description |
|---|---|
| [`evidence/1-scanning_output.txt`](../evidence/1-scanning_output.txt) | Full terminal output of the baseline scan |
| [`evidence/screenshots/1-scan-score.png`](../evidence/screenshots/1-scan-score.png) | Screenshot of the hardening index (56) |

![Baseline hardening index](../evidence/screenshots/1-scan-score.png)

## Next

[02 - Remediation Log](./02-remediation-log.md)