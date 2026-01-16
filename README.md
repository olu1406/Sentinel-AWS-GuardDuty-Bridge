# Sentinel-AWS-GuardDuty-Bridge
# The Cloud Bridge: AWS GuardDuty to Microsoft Sentinel

## Overview
This repository provides **ASIM-compliant parsers** that normalize AWS GuardDuty findings for Microsoft Sentinel. 

### Why this matters
In a multi-cloud SOC, analysts shouldn't have to learn different schemas for different clouds. By mapping GuardDuty to the **Advanced Security Information Model (ASIM)**, you can:
1. Use built-in Sentinel Analytics rules on AWS data.
2. Run cross-cloud hunting queries (e.g., "Find this IP in both Azure Firewall and AWS GuardDuty").
3. Visualize AWS threats using standard Sentinel Workbooks.

## Deployment

### Option 1: One-Click (Portal)
Click the button below to deploy these parsers as Functions into your Log Analytics Workspace:

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](Deploy/azuredeploy.json)

### Option 2: Azure CLI
You can also deploy via the command line. Ensure you update `<RG_NAME>` and `<WORKSPACE_NAME>`.

```bash
az deployment group create \
  --resource-group <RG_NAME> \
  --template-file Deploy/azuredeploy.json \
  --parameters workspaceName=<WORKSPACE_NAME>

  How to Use
Once deployed, you can query your AWS data using the normalized aliases.

1. Network Findings (SSH Brute Force, etc.)
Code snippet

AWSGuardDuty_Network
| where Severity >= 5.0
| summarize Count=count() by RemoteIp, Direction
2. Identity Findings (Suspicious Logins)
Code snippet

AWSGuardDuty_IAM
| where EventResult == "Failure"
| project TimeGenerated, UserName, ApiName, RemoteIp
Configuration
If your Log Analytics table is not named GuardDuty_CL, follow these steps:

Go to Sentinel > Logs.

Search for the function AWSGuardDuty_Config.

Edit the function code to match your table name:

Code snippet

print TableName = "YourCustomTable_CL", ...
Click Save. All parsers will update automatically.