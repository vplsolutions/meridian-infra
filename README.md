# Meridian Infra

Terraform-based infrastructure provisioning for the [Meridian](https://github.com/tvprasad/meridian) governed AI platform. Manages Azure resources consumed by [Meridian](https://github.com/tvprasad/meridian) (backend) and [Meridian Studio](https://github.com/tvprasad/meridian-studio) (frontend).

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## Resources Provisioned

| Resource | Terraform Type | Purpose |
|---|---|---|
| **Resource Group** | `azurerm_resource_group` | Container for all Meridian Azure resources |
| **Azure AI Services** | `azurerm_cognitive_account` | Multi-service Cognitive Services account covering Language, Vision, Speech, and Document Intelligence |

---

## Prerequisites

- [Terraform](https://developer.hashicorp.com/terraform/install) >= 1.5.0
- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) (`az login` authenticated)
- An active Azure subscription

---

## Getting Started

```bash
# 1. Clone and enter
git clone https://github.com/tvprasad/meridian-infra.git
cd meridian-infra

# 2. Configure variables
cp terraform.tfvars.example terraform.tfvars
# Edit terraform.tfvars — set your custom subdomain (must be globally unique)

# 3. Initialize and apply
terraform init
terraform plan
terraform apply
```

---

## Environment Configuration

After `terraform apply`, configure the Meridian backend `.env` with the outputs:

```bash
# Retrieve outputs
terraform output cognitive_account_endpoint
terraform output -raw cognitive_account_primary_key
```

All four Azure AI services share the single multi-service endpoint and key:

```env
AZURE_LANGUAGE_ENDPOINT=<cognitive_account_endpoint>
AZURE_LANGUAGE_KEY=<cognitive_account_primary_key>
AZURE_VISION_ENDPOINT=<cognitive_account_endpoint>
AZURE_VISION_KEY=<cognitive_account_primary_key>
AZURE_SPEECH_KEY=<cognitive_account_primary_key>
AZURE_SPEECH_REGION=eastus
AZURE_DOCUMENT_ENDPOINT=<cognitive_account_endpoint>
AZURE_DOCUMENT_KEY=<cognitive_account_primary_key>
```

---

## Variables

| Variable | Default | Description |
|---|---|---|
| `resource_group_name` | `meridian-rg` | Name of the Azure resource group |
| `location` | `eastus` | Azure region for all resources |
| `cognitive_account_name` | `meridian-ai-services` | Name of the Cognitive Services account |
| `cognitive_account_sku` | `S0` | SKU tier (S0 = standard multi-service) |
| `cognitive_custom_subdomain` | `meridian-ai` | Custom subdomain (must be globally unique) |
| `tags` | `project=meridian, managed_by=terraform` | Tags applied to all resources |

---

## Environments

Use `-var-file` to target a specific environment:

```bash
# Dev
terraform plan -var-file=environments/dev.tfvars

# Production
terraform plan -var-file=environments/prod.tfvars
```

---

## Project Structure

```
├── providers.tf                # Azure provider and version constraints
├── backend.tf                  # Remote state backend (commented out, start local)
├── variables.tf                # Input variables with defaults
├── main.tf                     # Resource Group + Azure AI Services
├── outputs.tf                  # Endpoint, keys, resource IDs
├── terraform.tfvars.example    # Example variable values
└── environments/
    ├── dev.tfvars              # Dev environment overrides
    └── prod.tfvars             # Prod environment overrides
```

---

## Related

- [meridian](https://github.com/tvprasad/meridian) — the backend RAG engine
- [meridian-studio](https://github.com/tvprasad/meridian-studio) — the operator UI

---

## License

[MIT](LICENSE) © Prasad Thiriveedi

> Microsoft Azure and all other third-party product names are trademarks or registered trademarks of their respective owners. Their use here does not imply endorsement or affiliation.
