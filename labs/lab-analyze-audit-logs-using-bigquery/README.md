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
1. **Generate account activity**
   - Create a storage bucket and upload a sample file.
   - Create a VPC network and a VM instance.
   - Remove the storage bucket.

2. **Export audit logs**
   - In Logs Explorer, run a query for activity logs.  
   - Create a sink named `AuditLogsExport`.  
   - Export to a new BigQuery dataset `auditlogs_dataset`.  

3. **Generate more activity**
   - Create additional buckets and upload files.  
   - Delete the VM instance.  
   - Remove the created buckets.  

4. **Switch to second user**
   - Log into the console using the second provided lab account.  

5. **Analyze Admin Activity Logs**
   - In Logs Explorer, search for `storage.buckets.delete`.  
   - Review matching entries and expand to see the `principalEmail`.  

6. **Use BigQuery**
   - Open `auditlogs_dataset` in BigQuery.  
   - Run SQL queries against the `cloudaudit_googleapis_com_activity` table to analyze recent activity such as VM deletion and bucket deletion.  

## Cloud Shell Commands
```bash
# Create a storage bucket in the project
gcloud storage buckets create gs://$DEVSHELL_PROJECT_ID

# Create a sample file and upload it to the bucket
echo "this is a sample file" > sample.txt
gcloud storage cp sample.txt gs://$DEVSHELL_PROJECT_ID

# Create a new VPC network
gcloud compute networks create mynetwork --subnet-mode=auto

# Capture the default compute zone into a variable
export ZONE=$(gcloud compute project-info describe --format="value(commonInstanceMetadata.items[google-compute-default-zone])")

# Create a VM instance in the new network
gcloud compute instances create default-us-vm --machine-type=e2-micro --zone=$ZONE --network=mynetwork

# Remove the storage bucket (cleanup)
gcloud storage rm --recursive gs://$DEVSHELL_PROJECT_ID

# Create additional buckets for more activity
gcloud storage buckets create gs://$DEVSHELL_PROJECT_ID
gcloud storage buckets create gs://$DEVSHELL_PROJECT_ID-test

# Create another sample file and upload it
echo "this is another sample file" > sample2.txt
gcloud storage cp sample.txt gs://$DEVSHELL_PROJECT_ID-test

# Delete the VM instance and its attached disks
export ZONE=$(gcloud compute project-info describe --format="value(commonInstanceMetadata.items[google-compute-default-zone])")
gcloud compute instances delete --zone=$ZONE --delete-disks=all default-us-vm
# Confirm deletion by entering Y when prompted

# Remove both storage buckets (cleanup)
gcloud storage rm --recursive gs://$DEVSHELL_PROJECT_ID
gcloud storage rm --recursive gs://$DEVSHELL_PROJECT_ID-test

```

### BigQuery SQL
```sql
-- Query to filter Cloud Audit Activity logs
logName = ("projects/PROJECT_ID/logs/cloudaudit.googleapis.com%2Factivity")
-- Identify VM deletion events in the last 7 days
SELECT
  timestamp,
  resource.labels.instance_id,
  protoPayload.authenticationInfo.principalEmail,
  protoPayload.resourceName,
  protoPayload.methodName
FROM
  `auditlogs_dataset.cloudaudit_googleapis_com_activity_*`
WHERE
  PARSE_DATE('%Y%m%d', _TABLE_SUFFIX) BETWEEN
  DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) AND CURRENT_DATE()
  AND resource.type = "gce_instance"
  AND operation.first IS TRUE
  AND protoPayload.methodName = "v1.compute.instances.delete"
ORDER BY timestamp, resource.labels.instance_id
LIMIT 1000;
-- Identify bucket deletion events in the last 7 days
SELECT
  timestamp,
  resource.labels.bucket_name,
  protoPayload.authenticationInfo.principalEmail,
  protoPayload.resourceName,
  protoPayload.methodName
FROM
  `auditlogs_dataset.cloudaudit_googleapis_com_activity_*`
WHERE
  PARSE_DATE('%Y%m%d', _TABLE_SUFFIX) BETWEEN
  DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) AND CURRENT_DATE()
  AND resource.type = "gcs_bucket"
  AND protoPayload.methodName = "storage.buckets.delete"
ORDER BY timestamp, resource.labels.instance_id
LIMIT 1000;
```

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
