# Wazuh Backup and Recovery Validation

## Overview

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

## Objectives

- Identify the installed Wazuh Agent configuration file.
- Create a timestamped backup without modifying the original configuration.
- Verify backup integrity using SHA-256 hashes.
- Simulate file recovery in a separate staging directory.
- Confirm that the recovered file matches the verified backup.
- Check the Wazuh Agent Windows service status.
- Validate the endpoint's reported status in the Wazuh Dashboard.
- Document findings, limitations, and recovery considerations.

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

## Conclusion

The selected Wazuh Agent configuration was backed up, and matching SHA-256 hashes confirmed the integrity of the backup and staged recovery copy. The Windows Agent service was running, and the endpoint appeared active in the Wazuh Dashboard during validation.

The results support successful file-level backup and recovery-copy validation, not a claim of complete Wazuh disaster-recovery readiness.
