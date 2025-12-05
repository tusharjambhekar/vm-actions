# vm-actions

Testing out GitHub Actions to create a virtual machine(VM) within Azure. 

## Overview

This repository demonstrates Infrastructure as Code (IaC) using GitHub Actions to automate the provisioning of Windows Virtual Machines in Microsoft Azure. The project uses Azure CLI commands within PowerShell scripts, orchestrated by GitHub Actions workflows to create cloud infrastructure automatically when code is pushed to the repository.

## Repository Structure

```
vm-actions/
├── .github/
│   └── workflows/
│       ├── main.yml       # GitHub Actions workflow (variant 1)
│       └── workflow.yml   # GitHub Actions workflow (variant 2)
├── IaC/
│   └── AzCLI/
│       └── vmcreation.ps1 # PowerShell script for VM provisioning
├── LICENSE                # MIT License
└── README.md             # This file
```

## Code Description

### PowerShell Script: `IaC/AzCLI/vmcreation.ps1`

This is the main Infrastructure as Code script that provisions a Windows Virtual Machine in Azure. The script:

**Parameters:**
- `servicePrincipal` - Azure Service Principal Application ID
- `servicePrincipalSecret` - Service Principal secret/password
- `servicePrincipalTenantId` - Azure tenant ID
- `azureSubscriptionName` - Target Azure subscription ID
- `resourceGroupName` - Name for the resource group to be created
- `resourceGroupNameRegion` - Azure region for resource deployment
- `serverName` - Name for the virtual machine
- `adminLogin` - Administrator username for the VM
- `adminPassword` - Administrator password for the VM

**Workflow:**
1. **Login**: Authenticates to Azure using Service Principal credentials via `az login`
2. **Set Subscription**: Sets the target Azure subscription using `az account set`
3. **Create Resource Group**: Creates a new resource group in the specified region using `az group create`
4. **Create VM**: Provisions a Windows Server 2019 Datacenter VM using `az vm create` with:
   - Windows Server 2019 Datacenter image
   - Specified administrator credentials
   - Error handling for existing VMs

### GitHub Actions Workflows

The repository contains two workflow files that automate the VM provisioning process:

#### `main.yml` and `workflow.yml`

Both workflows are nearly identical (with minor differences in admin username) and perform the following:

**Trigger:** Runs on every push to the repository (`on: [push]`)

**Job: DeployVM**
- Runs on: `windows-latest` runner
- Steps:
  1. **Checkout repo**: Uses `actions/checkout@v1` to clone the repository
  2. **Look for ps1 file**: Lists contents of the IaC/AzCLI directory to verify script presence
  3. **Provision VM**: Executes the PowerShell script with parameters from:
     - GitHub Secrets (for sensitive data)
     - Environment variables (for configuration)

**Environment Variables:**
- `RESOURCE_GROUP`: rg-githubdeploy
- `RESOURCE_GROUP_REGION`: ukwest
- `SERVER_NAME`: githubactions
- `ADMIN_LOGIN`: tushar (main.yml) / sarah (workflow.yml)

## Prerequisites

To use this repository, you need:

1. **Azure Account** with an active subscription
2. **Azure Service Principal** with appropriate permissions to create resources
3. **GitHub Repository Secrets** configured:
   - `SERVICE_PRINCIPAL_APPID` - Application ID of the service principal
   - `SERVICE_PRINCIPAL_SECRET` - Service principal password/secret
   - `SERVICE_PRINCIPAL_TENANTID` - Azure AD tenant ID
   - `AZURE_SUBSCRIPTION_ID` - Target Azure subscription ID
   - `ADMIN_PASSWORD` - Password for VM administrator account

## Setup Instructions

1. **Fork or Clone this repository**

2. **Create Azure Service Principal:**
   ```bash
   az ad sp create-for-rbac --name "github-actions-sp" --role contributor \
       --scopes /subscriptions/{subscription-id}
   ```

3. **Configure GitHub Secrets:**
   - Go to your repository Settings → Secrets and variables → Actions
   - Add the required secrets listed in Prerequisites

4. **Customize the workflow:**
   - Edit environment variables in `.github/workflows/main.yml` or `.github/workflows/workflow.yml`
   - Modify resource group name, region, server name, or admin login as needed

## Usage

Once configured, the workflow automatically triggers on every push to the repository:

1. Push code changes to the repository
2. GitHub Actions workflow starts automatically
3. The workflow provisions (or verifies) the VM in Azure
4. Check the Actions tab in GitHub to monitor progress and view logs

## Key Features

- **Automated Infrastructure Provisioning**: No manual Azure portal interaction needed
- **Idempotent Deployment**: Script handles existing VMs gracefully
- **Secure Credential Management**: Uses GitHub Secrets for sensitive information
- **Infrastructure as Code**: All infrastructure defined in version-controlled scripts
- **Windows-based Execution**: Runs on Windows runners for PowerShell compatibility

## Security Considerations

- All sensitive credentials are stored in GitHub Secrets (encrypted)
- Service Principal follows least-privilege principle
- Admin passwords are never exposed in logs or code
- Azure login credentials are passed securely to the PowerShell script

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Credits

Written by: Sarah Lean

Find me on:

* My Blog: <https://www.techielass.com>
* Twitter: <https://twitter.com/techielass>
* LinkedIn: <http://uk.linkedin.com/in/sazlean>
* Github: <https://github.com/weeyin83>
