# Wazuh-Backup-Recovery-Validation
## Overview
A backup is a separate copy of important configuration files or data that can be used if the original is accidentally deleted, corrupted, or needs to be recovered after a failure.

Recovery validation means checking whether a backup can be copied to a recovery location and whether the recovered copy matches the original backup. Creating a backup alone does not prove that recovery will work.

In a Wazuh environment, backup requirements depend on which component needs to be recovered:

- **Wazuh Agent:** Configuration files such as `ossec.conf`, which define settings for the endpoint agent.
- **Wazuh Manager:** Manager configuration, detection rules, decoders, and other relevant settings.
- **Wazuh Indexer:** Indexed security data and the mechanisms required to restore it.
- **Wazuh Dashboard:** Dashboard configuration and related settings.

### Important Concepts

| Concept | Simple Explanation |
|---|---|
| **Backup creation** | Copying configuration files or data to a separate location. |
| **Integrity verification** | Comparing SHA-256 hashes to check whether file contents match. |
| **Recovery testing** | Copying a backup into a separate staging directory and checking whether the recovered copy matches the backup. |
| **Operational validation** | Checking whether the Wazuh Agent service is running and whether the endpoint appears active in the dashboard. |
| **Recovery limitation** | Matching hashes proves file-content equality, not that the configuration is valid or that the entire Wazuh environment can be recovered. |

### Example

Suppose `ossec.conf` is accidentally damaged. A backup exists, but its integrity and recovery usability have not been verified.

You calculate the SHA-256 hash of the backup, copy it into a separate test directory, and compare the recovered file's hash with the backup hash. If the hashes match, you have evidence that the recovered copy contains the same data as the backup.

This validates the file-copy recovery process without risking the active Wazuh Agent configuration.

This lab validates the backup integrity and staged recovery of a Windows Wazuh Agent configuration file. The investigation uses PowerShell to identify the configuration, create a separate backup, compare SHA-256 hashes, simulate recovery into a staging directory, and verify the agent's operational status.

The objective is to determine whether the selected configuration file was copied successfully and whether its contents remained unchanged throughout the backup and recovery workflow.

## Environment

- **Endpoint:** Windows 11 Pro
- **PowerShell:** 7.6.9
- **Wazuh Agent:** 4.12.0
- **Wazuh Agent ID:** 001
- **Endpoint hostname:** `DESKTOP-9MMM37V`
- **Endpoint IP:** `192.168.203.1`
- **Configuration file:** `C:\Program Files (x86)\ossec-agent\ossec.conf`
  
## Lab Objectives

- Identify the Windows Wazuh Agent configuration file and record its relevant metadata.
- Create a timestamped backup of `ossec.conf` in a separate directory without modifying the active configuration.
- Verify backup integrity by comparing the SHA-256 hashes of the original configuration and its backup.
- Simulate recovery by copying the backup into a separate staging directory.
- Validate the recovered copy by comparing its SHA-256 hash with the verified backup.
- Check the Windows Wazuh Agent service status and confirm the endpoint's reported status in the Wazuh Dashboard.
- Document the validation results, supporting evidence, and limitations of the file-level recovery test.
- Distinguish successful backup and recovery-copy validation from full Wazuh platform disaster-recovery readiness.

## Lab Scenario

A security analyst is validating the backup and recovery process for a Windows endpoint monitored by Wazuh. The endpoint's `ossec.conf` file contains configuration settings required by the Wazuh Agent, so maintaining a reliable backup is important if the original file is accidentally modified, corrupted, or deleted.

The analyst locates the configuration file, records its metadata, and creates a timestamped backup in a separate directory. To verify the backup's integrity, SHA-256 hashes are calculated for the original and backup files and compared. Matching hashes provide evidence that both files contain identical data at the time of verification.

The analyst then simulates recovery by copying the backup into a separate staging directory instead of overwriting the active configuration. The recovered file's hash is compared with the backup hash to determine whether the recovery copy matches the verified backup. Finally, the analyst checks the Windows Wazuh Agent service and the endpoint's status in the Wazuh Dashboard to document the system's operational state.

The investigation focuses on the following activities:

- Identify and back up the Windows Wazuh Agent configuration file.
- Verify backup integrity using SHA-256 hash comparisons.
- Validate file-copy recovery in a separate staging directory.
- Record the agent service state and dashboard-reported endpoint status.
- Document the results, supporting evidence, and limitations of the recovery test.

The active configuration is not overwritten, and no service restart is performed as part of this exercise. The findings establish whether the selected configuration file was backed up and recovered consistently. They do not demonstrate full Wazuh disaster recovery or prove that the configuration will function correctly after restoration.

## Investigation Workflow

1. Locate `ossec.conf` and record its path, size, and modification time.
2. Create a timestamped backup under the current user's profile.
3. Calculate SHA-256 hashes for the original and backup files.
4. Compare the hashes to verify file-content equality.
5. Copy the backup into a separate `recovery-test` directory.
6. Compare the recovered file's hash against the backup hash.
7. Check the Windows Wazuh Agent service status.
8. Confirm the endpoint status in the Wazuh Dashboard.
9. Document the results and scope limitations.

## Backup Location

The backup was created at:

`C:\Users\Dell\Wazuh-Backup-Lab121\20261029-071243\ossec.conf`

The recovery test copy was created in the `recovery-test` subdirectory of the same timestamped backup folder.

## Validation Results

| Validation | Observed result |
|---|---|
| Configuration discovery | Successful |
| Original configuration size | 10,059 bytes |
| Backup creation | Successful |
| Original and backup SHA-256 comparison | Matching hashes |
| Staged recovery copy | Created successfully |
| Backup and recovered-copy SHA-256 comparison | `Match = True` |
| Windows service | `WazuhSvc` — Running |
| Wazuh Dashboard | Agent `001` displayed as active |

### SHA-256 Integrity Result

The original configuration and backup produced the same SHA-256 hash:

`A3C8010CE63BA05F18972D4EA4582C1B96A0CB40B7052DF6AEBD7FA672A8F72B`

The recovered file also produced the same hash as the backup. This confirms that the file contents matched across the tested copies.

## Key Learnings

- A backup should be verified rather than assumed to be usable.
- SHA-256 comparisons provide evidence of file-content equality.
- A staged recovery test reduces the risk of accidentally overwriting active configuration.
- Service status and dashboard status provide operational context but do not independently validate the configuration's correctness.
- File-level recovery validation is different from full-platform disaster recovery.

## Scope and Limitations

This lab validates one Windows Wazuh Agent configuration file. The recovery exercise copied the backup into a separate directory; the active configuration was not overwritten.

The investigation did not test a full Wazuh Manager, Indexer, or Dashboard recovery, restore indexed security data, or validate all configuration dependencies. Matching hashes do not prove that the configuration is syntactically valid or that a future service restart would succeed.

