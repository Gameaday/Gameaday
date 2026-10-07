# Runbook: Verify Backups

The whole system rests on irreplaceable family data (photos/videos/documents) being safe. A backup
you have not tested is not a backup. This runbook is the recurring verification procedure.

## Cadence

| Frequency | Check | Where |
|-----------|-------|-------|
| Daily (automated) | Synology snapshot completed without error | Synology Hyper Backup / Snapshot Replication |
| Weekly (manual, ~10 min) | Restore a random sample of files to a scratch location and open them | old laptop |
| Monthly (manual, ~15 min) | Confirm offsite (Glacier) copy is current and intact | AWS console / rclone check |
| Quarterly (manual) | Full restore drill of one folder end-to-end | old laptop |

## Weekly sample restore

1. Pick a random recent date/folder in the source (e.g. a phone photo folder).
2. Restore it from the Synology snapshot to a scratch folder on the old laptop.
3. Open 3-5 files and confirm they are valid (not zero-byte, not corrupt).
4. Delete the scratch copy.
5. Note the result (pass/fail) in the log below.

## Monthly offsite check

1. Confirm the last offsite upload completed (no errors, expected size).
2. Run a metadata-only check (e.g. `rclone check --size-only` or `restic check`) against Glacier.
3. Confirm retention/lifecycle rules are still as intended.
4. Note the result.

## 3-2-1 rule (must hold at all times)

- **3** copies of irreplaceable data
- **2** different media/locations
- **1** offsite

If any of these is not true, treat it as a 🔴 incident and fix before anything else.

## Verification log

| Date | Type | Result | Notes |
|------|------|--------|-------|
| (add entries) | | | |

## Escalation

If a check fails: stop adding new services, fix backup integrity first, and record what was lost
or at risk. This is the one area where "it'll be fine" is not acceptable.
