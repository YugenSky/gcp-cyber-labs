# Determine the difference between normal activity and an incident

## Objectives
- Review logs and system events
- Classify suspicious activity vs normal baselines
- Escalate true incidents for investigation

## Environment
- Google Cloud project (sandbox or test)
- IAM roles scoped minimally for tasks
- Cloud Shell or local terminal with `gcloud`

## Steps
1. **Grant an external account temporary permissions**
   - Console: IAM & Admin → IAM → **Grant Access**
   - New principal: `bad.actor.demo@gmail.com`
   - Role: **Basic → Owner**
   - Click **Save**

2. **Review Event Threat Detection (ETD) findings**
   - Console: Security → **Findings**
   - Locate findings labeled **Persistence: IAM anomalous grant**

3. **Analyze the finding details**
   - Open the earliest **Persistence: IAM Anomalous Grant** finding
   - Note the **principal email** (the normal qwiklabs.net user)
   - Go to **Source properties** → expand **sensitiveRoleGrant → members**
   - Compare entries from the normal user vs the external user

4. **Correlate the finding in Cloud Logging**
   - Open **Logs Explorer**
   - Pin **Observability Logging** for quick access
   - Run the IAM-related audit log filter (see Queries below)
   - Expand the matching entry and open **nested fields** for detail

5. **Remediate**
   - Console: IAM & Admin → IAM
   - Next to `bad.actor.demo@gmail.com` click **Edit principal**
   - Click the **trash** icon to remove **Owner**
   - Click **Save**

## Commands and Queries
-- Logs Explorer (Advanced filter)
-- Purpose: Show audit events related to project ownership/invite actions that align with the ETD “Persistence: IAM Anomalous Grant” finding.
-- (Paste ONLY the lines below into Logs Explorer; comments are here in the README for context.)
```
protoPayload.authorizationInfo.permission="resourcemanager.projects."
protoPayload.methodName="InsertProjectOwnershipInvite"

```


## Reflection
Gained experience classifying events vs. incidents, building consistency in triage decisions, and documenting rationale for escalation.

## Skills and Tags
- Skills: Alert triage, Incident classification, SOC runbooks
- MITRE ATT&CK: Initial Access, Execution, Detection
- Tools: Google Cloud Logging, Cloud Audit Logs, BigQuery, gcloud
