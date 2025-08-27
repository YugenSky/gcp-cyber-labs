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
1. Start the lab and open the Google Cloud Console using the provided lab credentials.  
2. Enable the Key Management Service (KMS) API if not already enabled.  
3. Create a key ring:  
   - Name: `demo-key-ring`  
   - Location type: Region  
4. Inside the key ring, create a symmetric key:  
   - Name: `demo-key`  
   - Purpose: Symmetric encrypt/decrypt  
   - Key rotation period: 90 days  
5. Create an asymmetric key in the same key ring:  
   - Name: `demo-asymmetric-key`  
   - Purpose: Asymmetric decrypt  
6. Verify both keys are created and visible in the KMS console.  
   - Symmetric key shows purpose: *Symmetric encrypt/decrypt*  
   - Asymmetric key shows purpose: *Asymmetric decrypt*  
7. End the lab and submit to complete.  

## Commands and Queries
No CLI or SQL commands were used in this lab. All tasks were performed via the Google Cloud Console UI._

## Reflection
Learned the difference between symmetric and asymmetric key use cases, practiced key rotation, and reinforced the importance of least-privilege IAM for KMS.

## Skills and Tags
- Skills: KMS, Cryptography, Key lifecycle
- MITRE ATT&CK: Credential Access, Defense Evasion
- Tools: Google Cloud Logging, Cloud Audit Logs, BigQuery, gcloud
