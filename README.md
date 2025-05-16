# TerraSnap-Govern

A comprehensive solution for identifying and managing orphaned Azure snapshots - those whose source disks no longer exist. This tool helps reduce unnecessary storage costs, improve resource management, and address potential compliance issues.

## Features

- **Orphaned Snapshot Detection**: Automatically identifies snapshots with non-existent source disks
- **Multi-subscription Support**: Scans across all accessible subscriptions or a specific one
- **Flexible Authentication**: Supports Azure CLI, Managed Identity, and Service Principal authentication
- **Reporting Options**: Detailed console output and JSON export capability
- **Safe Operations**: Default dry-run mode prevents accidental deletions
- **Cross-platform**: Python and Bash implementations available

## Prerequisites

- **For Python script**:
  - Python 3.6+
  - Azure SDK for Python
  - Appropriate Azure RBAC permissions
  
- **For Bash script**:
  - Azure CLI (az) installed and configured
  - jq utility for JSON processing
  - Appropriate Azure RBAC permissions

## Installation

### Option 1: Clone Repository

```bash
git clone https://github.com/your-org/azure-snapshot-cleanup.git
cd azure-snapshot-cleanup

# Install Python dependencies
pip install -r requirements.txt
```

## Quick Start

### Python Script

```bash
# Scan all accessible subscriptions (dry run mode)
python scripts/azure_snapshot_cleanup.py --auth-method cli --dry-run

# Export results to JSON
python scripts/azure_snapshot_cleanup.py --export results.json

# Delete orphaned snapshots (use with caution!)
python scripts/azure_snapshot_cleanup.py --delete
```

### Bash Script

```bash
# Scan all accessible subscriptions (dry run mode)
./scripts/azure_snapshot_cleanup.sh

# Export results to JSON
./scripts/azure_snapshot_cleanup.sh --export results.json

# Delete orphaned snapshots (use with caution!)
./scripts/azure_snapshot_cleanup.sh --delete --no-dry-run
```

## 📊 Usage Examples

### Basic Scanning

```bash
# Python script
python scripts/azure_snapshot_cleanup.py

# Bash script
./scripts/azure_snapshot_cleanup.sh
```

### Targeting Specific Subscription

```bash
# Python script
python scripts/azure_snapshot_cleanup.py --subscription-id "00000000-0000-0000-0000-000000000000"

# Bash script
./scripts/azure_snapshot_cleanup.sh -s "00000000-0000-0000-0000-000000000000"
```

### Using Service Principal Authentication

```bash
python scripts/azure_snapshot_cleanup.py \
  --auth-method service-principal \
  --sp-client-id "YOUR_CLIENT_ID" \
  --sp-client-secret "YOUR_CLIENT_SECRET" \
  --sp-tenant-id "YOUR_TENANT_ID"
```

### Automated Cleanup Job

```bash
python scripts/azure_snapshot_cleanup.py \
  --auth-method service-principal \
  --sp-client-id "$SP_CLIENT_ID" \
  --sp-client-secret "$SP_CLIENT_SECRET" \
  --sp-tenant-id "$SP_TENANT_ID" \
  --delete \
  --export "cleanup-$(date +%Y%m%d).json"
```

## 🔍Authentication Methods

| Method | Description | Best For |
|--------|-------------|----------|
| CLI | Uses existing Azure CLI login | Interactive use, local development |
| Managed Identity | Uses Azure-managed identities | Azure VMs, App Services, Azure Functions |
| Service Principal | Uses application credentials | Automation, CI/CD pipelines |

## Command Line Options

### Python Script

```
--auth-method {cli,managed-identity,service-principal}
                      Authentication method (default: cli)
--sp-client-id SP_CLIENT_ID
                      Service Principal Client ID
--sp-client-secret SP_CLIENT_SECRET
                      Service Principal Client Secret
--sp-tenant-id SP_TENANT_ID
                      Service Principal Tenant ID
--subscription-id SUBSCRIPTION_ID
                      Specific subscription ID to scan
--delete              Delete orphaned snapshots (default: report only)
--dry-run             Perform a dry run (don't actually delete snapshots)
--export EXPORT       Export results to a JSON file
--log-level {DEBUG,INFO,WARNING,ERROR}
                      Set logging level (default: INFO)
```

### Bash Script

```
-h, --help                 Show this help message
-s, --subscription ID      Specific subscription ID to scan
-d, --delete               Delete orphaned snapshots (default: report only)
--no-dry-run               Actually perform deletions (default: dry run)
-e, --export FILE          Export results to a JSON file
-l, --log FILE             Log file (default: snapshot_cleanup_<timestamp>.log)
-v, --verbose              Enable verbose output
```

## Project Structure

```
azure-snapshot-cleanup/
│
├── scripts/                      # Executable scripts
│   ├── azure_snapshot_cleanup.py # Main Python script
│   └── azure_snapshot_cleanup.sh # Alternative Bash script
│
├── function_app/                 # Azure Function implementation
│   ├── host.json
│   ├── requirements.txt
│   └── SnapshotCleanup/
│       ├── __init__.py
│       └── function.json
│
├── terraform/                    # Terraform deployment configurations
│   ├── modules/
│   └── examples/
│
└── docs/                         # Documentation
    ├── usage_guide.md
    └── troubleshooting.md
```

## Security Considerations

- **Least Privilege**: Use RBAC roles with minimal required permissions
- **Secure Credentials**: Never hardcode service principal secrets; use environment variables or Azure Key Vault
- **Access Review**: Regularly review who has access to run this tool, especially in deletion mode
- **Audit Trail**: Use the `--export` option to maintain records of identified and deleted resources

## Deployment Options

### As a Scheduled Task

1. Create a VM or container with the necessary authentication
2. Set up a cron job or scheduled task to run the script periodically
3. Consider using the `--export` flag to maintain an audit trail

### As an Azure Function

Deploy the provided function app for serverless execution:

1. Deploy using the Azure CLI:
   ```bash
   cd function_app
   func azure functionapp publish your-function-app-name
   ```

2. Or use the included Terraform configurations:
   ```bash
   cd terraform/examples/function
   terraform init
   terraform apply
   ```

## Additional Documentation

- [Usage Guide](docs/usage_guide.md) - Detailed instructions for using the tool
- [Troubleshooting](docs/troubleshooting.md) - Solutions for common issues
- [License](LICENSE) - Project license information

