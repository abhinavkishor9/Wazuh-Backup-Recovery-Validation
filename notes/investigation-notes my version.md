# Investigation Notes 
## 1. Configuration Discovery

The configuration was located at:

`C:\Program Files (x86)\ossec-agent\ossec.conf`

Observed metadata:

- File size: 10,059 bytes
- Last modified: 15 October 2026, 06:34:50
- PowerShell version: 7.6.9

The file was found using a PowerShell search across standard Wazuh Agent installation locations.

## 2. Backup Creation

A timestamped backup directory was created:

`C:\Users\Dell\Wazuh-Backup-Lab121\20261029-071243`

The configuration was copied to:

`C:\Users\Dell\Wazuh-Backup-Lab121\20261029-071243\ossec.conf`

The resulting backup file was reported as 10,059 bytes.

The backup's reported modification time matched the original file's displayed modification time. This is consistent with copying the file, but file metadata alone does not establish content integrity.

## 3. Integrity Verification

SHA-256 hashes were calculated for the original configuration and its backup.

**Observed SHA-256 hash:**

`A3C8010CE63BA05F18972D4EA4582C1B96A0CB40B7052DF6AEBD7FA672A8F72B`

The original and backup hash comparison indicated matching contents. The recovery-test output subsequently showed the backup and recovered file hashes matching, with `Match = True`.

### Interpretation

The matching hashes establish that the tested copies contained identical file contents at the time of comparison. They do not establish that the file is free of configuration errors or that it represents a known-good historical state.

## 4. Staged Recovery Validation

A separate directory named `recovery-test` was created under the timestamped backup directory.

The backup was copied to:

`recovery-test\ossec.conf`

The recovered file's SHA-256 hash matched the backup hash.

This demonstrates successful file-copy recovery into the test location. The active configuration was not replaced, and no service restart was required for this test.

## 5. Operational Validation

### Windows service

The PowerShell service query returned:

- Service name: `WazuhSvc`
- Display name: `Wazuh`
- Status: `Running`

### Wazuh Dashboard

The dashboard displayed:

- Agent ID: `001`
- Agent name: `DESKTOP-9MMM37V`
- IP address: `192.168.203.1`
- Agent version: `4.12.0`
- Status: `active`

These observations provide evidence that the agent service was running and the dashboard reported the endpoint as active during the investigation.

## 6. Evidence-Based Findings

### Confirmed

- The Wazuh Agent configuration file was located.
- A separate backup copy was created.
- The original and backup SHA-256 hashes matched.
- The staged recovery copy matched the backup hash.
- The Windows Wazuh Agent service was running.
- The Wazuh Dashboard displayed the endpoint as active.

### Not established by this test

- Whether the configuration would remain valid after future changes.
- Whether the agent would restart successfully using a restored configuration.
- Whether the Manager, Indexer, and Dashboard could be recovered from backups.
- Whether historical security data and all required certificates, secrets, and dependencies could be restored.
- Whether the backup would remain available after loss of the endpoint or user profile.

