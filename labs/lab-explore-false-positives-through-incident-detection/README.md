# Explore false positives through incident detection

## Objectives
- Investigate alerts in monitoring systems
- Differentiate between false positives and real threats
- Tune detection rules to reduce noise

## Environment
- Google Cloud project (sandbox or test)
- IAM roles scoped minimally for tasks
- Cloud Shell or local terminal with `gcloud`

## Steps
1. Preparation and access checks
2. Main exercise procedures
3. Validation
4. Cleanup
5. Lessons learned

Replace these with the exact steps you performed. If a step used console UI, include the path. If CLI, include the commands.

## Commands and Queries

### gcloud
```bash
# Set project
gcloud config set project <PROJECT_ID>

# Example: list auth accounts
gcloud auth list
```

### BigQuery SQL
```sql
-- Example: recent audit log events
SELECT
  protopayload_auditlog.methodName,
  protopayload_auditlog.authenticationInfo.principalEmail,
  timestamp
FROM `PROJECT_ID.cloudaudit_googleapis_com_data_access.events_*`
WHERE timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 7 DAY)
LIMIT 100;
```

## Reflection
Improved detection accuracy by investigating alerts, reducing false positives through rule tuning, and measuring before/after impact.

## Skills and Tags
- Skills: Alert investigation, Detection tuning, SOC analysis
- MITRE ATT&CK: Detection, Discovery
- Tools: Google Cloud Logging, Cloud Audit Logs, BigQuery, gcloud
