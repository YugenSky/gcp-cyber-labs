# Create symmetric and asymmetric keys

## Objectives
- Generate and manage symmetric keys
- Create asymmetric key pairs
- Understand cryptographic key usage in cloud environments

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

## Artifacts
- Place screenshots in `./evidence/`
- Place JSON, CSV, or exports in `./artifacts/`

## Reflection
- What worked well
- What to change next time
- Detection and response improvements

## Skills and Tags
- Skills: KMS, Cryptography, Key lifecycle
- MITRE ATT&CK: Credential Access, Defense Evasion
- Tools: Google Cloud Logging, Cloud Audit Logs, BigQuery, gcloud
