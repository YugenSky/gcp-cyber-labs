# Recover VMs with Google

## Objectives
- Perform VM snapshot- and backup-based recovery
- Validate system functionality after restore
- Document recovery procedures

## Environment
- Google Cloud project (sandbox or test)
- IAM roles scoped minimally for tasks
- Cloud Shell or local terminal with `gcloud`

## Steps
1. **Connect to Backup and DR console**
   - Search for *Backup and DR* in the Cloud Console.
   - Pin the service for quick access.
   - From the navigation pane, open **Management console** → **Log in to the management console**.
   - Use lab-provided account if prompted and skip the welcome tour.
   - In the title bar, go to **Manage → Appliances**.

2. **Create a backup plan template**
   - Title bar: **Backup Plans → Templates → +Create Template**.
   - Name: `vm-backup` | Description: *Virtual Machine Backups*.
   - Add a snapshot policy:
     - Policy Name: `Daily VM snapshot`
     - Scheduling: Continuous, every 2 hours
   - Save the template and confirm creation.

3. **Validate service account permissions**
   - In the Cloud Console, navigate to **IAM & Admin → IAM**.
   - Locate the service account for the backup and recovery appliance.
   - Confirm the role **Backup and DR Cloud Storage Operator** is assigned.

4. **Discover and add Compute Engine instances**
   - In the Backup and DR console, go to **Backup & Recover → Back Up**.
   - Under *Google Cloud → Compute Engine*, choose the backup credential.
   - Search for instances, select `lab-vm`, and apply the `vm-backup` template.
   - Finish onboarding and monitor the backup job.

5. **Restore a Compute Engine instance**
   - Backup & Recover → **Recover** → select `lab-vm` → Next.
   - In the action bar, choose **Table**, select snapshot image → **Mount**.
   - Choose **Mount as new GCE instance**.
   - Configuration:
     - Region: REGION
     - Zone: ZONE
     - Instance name: `lab-vm-recovered`
   - Confirm mount and verify in Job Monitor.
   - Check in Compute Engine: instances list should show `lab-vm`, `lab-vm-recovered`, and `qwiklabs-appliance`.

6. **Restore to an alternate project**
   - In **IAM & Admin → IAM**, copy the service account for Backup and DR.
   - Switch to **Project PROJECT_ID_2**.
   - Grant that service account the roles:
     - **Backup and DR Compute Engine Operator**
     - **Backup and DR Cloud Storage Operator**
   - Back in Backup and DR console, go to **Recover** → select `lab-vm` → Next.
   - Select snapshot image → **Mount as new GCE instance**.
   - Configuration:
     - Project: `PROJECT_ID_2`
     - Instance name: `lab-vm-project2`
     - Region: REGION | Zone: ZONE
   - Confirm mount and verify `lab-vm-project2` is created in Project 2’s VM instances list.

## Commands and Queries

No gcloud CLI commands or SQL queries were used in this lab. All steps were performed via the Google Cloud Console UI.

## Reflection
Practiced disaster recovery using snapshots and backups, validated restored services, and noted gaps like IAM and network settings.

## Skills and Tags
- Skills: BCDR, Snapshot/backup restore, Post-incident validation
- MITRE ATT&CK: Impact, Recovery
- Tools: Google Cloud Logging, Cloud Audit Logs, BigQuery, gcloud
