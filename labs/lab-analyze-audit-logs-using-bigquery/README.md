# Analyze audit logs using BigQuery

## Objectives
- Ingest audit logs into BigQuery
- Write queries to detect anomalies
- Create detections from audit events

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

## Reflections
**What worked well**
- Built reusable SQL snippets to filter by principal, resource, and method names
- Identified outlier behaviors using time windows and simple aggregations

**Challenges**
- Navigating schema variations across audit log tables and partitions

**Improvements implemented**
- Created a small library of parameterized queries with comments
- Added a normalization layer for common fields to simplify joins and filters

**Impact**
- Faster investigations and a foundation for detection engineering using auditable, shareable queries

## Skills and Tags
- Skills: Audit log analysis, Detection engineering, SQL
- MITRE ATT&CK: Discovery, Defense Evasion, Collection
- Tools: Google Cloud Logging, Cloud Audit Logs, BigQuery, gcloud
