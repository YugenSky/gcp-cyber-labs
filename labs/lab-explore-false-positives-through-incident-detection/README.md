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
1. **Create a new service account**
   - Console: IAM & Admin → Service Accounts → **+ Create Service Account**  
   - Name: `test-account`  
   - Role: **Basic → Owner**  
   - Click **Create and Continue → Done**

2. **Generate a JSON key for the service account**
   - Open the `test-account` service account details  
   - Actions (⋮) → Manage keys → **Add Key → Create new key**  
   - Key type: JSON → Download  
   - Rename downloaded file to `test-account.json`  
   - Open Cloud Shell → More (⋮) → Upload → select `test-account.json`  
   - Run `ls` in Cloud Shell to confirm the file uploaded

3. **Trigger the false positive detection**
   - Export project and service account variables  
   - Activate the service account using the JSON key  
   - List active credentials to confirm  
   - Grant a second user (`Google Cloud username 2`) the **roles/editor** role in the project

4. **Switch to the second user**
   - In the console, add the second account using lab-provided credentials  
   - Sign in and switch to this account

5. **Review the detection finding**
   - Console: Security → Findings → Quick filters → **User managed service account key**  
   - Open the most recent finding  
   - Review details under **Summary** tab (severity, threat class, compliance tab info)

6. **Remediate**
   - Console: IAM & Admin → Service Accounts → select `test-account`  
   - Go to the **Keys** tab  
   - Delete the JSON key associated with this service account

## Commands and Queries

### Cloud Shell Commands

```bash
# Store current project ID in a variable
export PROJECT_ID=$(gcloud info --format="value(config.project)")

# Store the full email of the service account
export SA_NAME="test-account@${PROJECT_ID}.iam.gserviceaccount.com"

# Authenticate using the service account JSON key
gcloud auth activate-service-account ${SA_NAME} --key-file=test-account.json

# Verify which accounts are currently authenticated
gcloud auth list

# Grant the second lab user the Editor role on the project
export STUDENT2="Google Cloud username 2"
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member user:${STUDENT2} \
  --role roles/editor
  
```

## Reflection
Improved detection accuracy by investigating alerts, reducing false positives through rule tuning, and measuring before/after impact.

## Skills and Tags
- Skills: Alert investigation, Detection tuning, SOC analysis
- MITRE ATT&CK: Detection, Discovery
- Tools: Google Cloud Logging, Cloud Audit Logs, BigQuery, gcloud
