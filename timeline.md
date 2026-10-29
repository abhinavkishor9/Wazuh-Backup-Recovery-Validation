
# Timeline — Wazuh Backup and Recovery Validation

## Investigation Sequence

| Stage | Activity | Evidence / Result |
|---|---|---|
| 1 | Located the Wazuh Agent configuration | `ossec.conf` found under `C:\Program Files (x86)\ossec-agent\` |
| 2 | Recorded source-file metadata | Size: 10,059 bytes; last modified: 15 October 2026, 06:34:50 |
| 3 | Created a timestamped backup directory | `Wazuh-Backup-Lab121\20261029-071243` |
| 4 | Copied the configuration to the backup location | Backup file created; reported size: 10,059 bytes |
| 5 | Calculated SHA-256 hashes | Original and backup hashes matched |
| 6 | Created a separate recovery-test directory | Staging directory created under the timestamped backup folder |
| 7 | Copied the backup into the recovery-test directory | Recovered configuration file created |
| 8 | Compared backup and recovery-copy hashes | `Match = True` |
| 9 | Checked the Windows Wazuh Agent service | `WazuhSvc` reported as `Running` |
| 10 | Checked the Wazuh Dashboard | Agent `001` displayed as `active` |

## Timestamp Evidence

The backup directory name contains the timestamp:

`20261029-071243`

This corresponds to 29 October 2026 at 07:12:43, based on the timestamp format used by the PowerShell script.

The original configuration's displayed last-modified time was 15 October 2026 at 06:34:50.

Exact execution times for the remaining investigation steps were not captured in the supplied evidence and are therefore not inferred here.

## Outcome

The backup and staged recovery copy had matching SHA-256 hashes. The Windows Wazuh Agent service was running, and the dashboard reported the endpoint as active.

The investigation validated file-level backup integrity and staged recovery-copy integrity. Full Wazuh platform disaster recovery remains outside the scope of this lab.
