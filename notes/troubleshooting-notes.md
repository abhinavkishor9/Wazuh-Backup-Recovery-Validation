
# Troubleshooting Notes — Wazuh Backup and Recovery Validation

## Purpose

This document records troubleshooting considerations for locating, backing up, verifying, and recovering the Windows Wazuh Agent configuration file.

The lab completed its backup and staged recovery checks without needing to overwrite the active configuration or restart the service.

## 1. Configuration File Not Found

### Symptom

PowerShell cannot locate `ossec.conf` in the standard installation directories.

### Possible causes

- Wazuh Agent is installed in a different directory.
- A custom installation path was used.
- The agent is not installed on the endpoint.

### Troubleshooting

1. Check the Wazuh Agent installation directory.
2. Inspect the registered Wazuh Windows service.
3. Confirm the actual configuration path before creating a backup.
4. Do not create an empty replacement configuration to work around a missing file.

### Lab observation

The configuration was found at:

`C:\Program Files (x86)\ossec-agent\ossec.conf`

No path-discovery failure was observed.

## 2. Backup Creation Failure

### Symptom

`Copy-Item` fails to create the backup.

### Possible causes

- The source path is incorrect.
- The destination directory cannot be created.
- The current user lacks the required permissions.
- The destination file is unavailable or inaccessible.

### Troubleshooting

1. Confirm the source file exists.
2. Check the destination path and available disk space.
3. Review the PowerShell error details.
4. Verify that the backup file exists and inspect its size after copying.
5. Do not assume success merely because the command was executed.

### Lab observation

The backup was created under the user's profile and reported a size of 10,059 bytes.

## 3. SHA-256 Hashes Do Not Match

### Symptom

The original and backup files produce different SHA-256 hashes.

### Possible causes

- The files contain different data.
- The source changed between copying and hash calculation.
- An incorrect source or destination file was selected.
- The copy operation did not complete as expected.

### Troubleshooting

1. Recheck both file paths.
2. Recalculate both hashes.
3. Compare file sizes and modification times as supporting evidence.
4. If necessary, create a new backup and verify it again.
5. Do not label a backup as verified until the hash comparison succeeds.

### Lab observation

The original and backup hashes matched. The staged recovery copy also matched the backup hash.

## 4. Recovery Copy Does Not Match the Backup

### Symptom

The recovered file's SHA-256 hash differs from the backup hash.

### Troubleshooting

1. Confirm that the recovery destination is correct.
2. Verify the source backup's hash again.
3. Recopy the backup into a clean staging directory.
4. Recalculate the recovered file's hash.
5. Preserve the failed test output for investigation.

### Lab observation

The recovery comparison returned `Match = True`.

## 5. Wazuh Agent Service Is Not Running

### Symptom

The Windows service query reports that the agent is stopped or cannot find the expected service.

### Troubleshooting

1. Confirm the registered service name and display name.
2. Check the service state and relevant Windows event logs.
3. Review Wazuh Agent logs for errors.
4. Do not replace the active configuration with a backup solely because the service is stopped.
5. Investigate the service issue separately before attempting a controlled recovery.

### Lab observation

`WazuhSvc` was reported as `Running`.

## 6. Dashboard Shows the Agent as Inactive

### Symptom

The Wazuh Dashboard does not show the endpoint as active.

### Possible areas to investigate

- Agent-to-manager connectivity.
- Agent service state.
- Manager-side agent status.
- Network changes or communication errors.
- Whether the dashboard data is current.

### Troubleshooting

Compare the Windows service state with the manager-side agent status and relevant logs. Treat dashboard status as one source of evidence rather than proof of the underlying cause.

### Lab observation

Agent `001`, `DESKTOP-9MMM37V`, was displayed as `active` in the dashboard.

## 7. Important Recovery Limitations

A matching hash proves file-content equality; it does not prove configuration validity or successful service startup after restoration.

This lab did not overwrite the live configuration or test a full platform recovery. A production recovery plan should include appropriate backups of the Wazuh Manager, Indexer, Dashboard, required secrets, certificates, and security data, followed by restoration tests in a controlled environment.

## Final Status

The selected configuration backup passed the observed integrity and staged recovery-copy checks. The Wazuh Agent service was running, and the endpoint was displayed as active.

No failed hash comparison or backup-copy error was reported in the supplied evidence.
