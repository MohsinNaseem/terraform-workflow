# terraform-workflow

Reusable GitHub Actions workflows for opsai Terraform infrastructure.

## Available Workflows

| Workflow | Purpose | Called By |
|---|---|---|
| `terraform-stable.yml` | Plan + Apply for all infra repos | Every infra repo |
| `terraform-destroy-stable.yml` | Plan destroy + Destroy (non-live only) | Infra repos |
| `terraform-stable-management-account.yml` | Management account changes | opsai-organization-bootstrap |

## Usage in App Repos

### Deploy infrastructure
```yaml
name: Deploy infrastructure

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy the infrastructure.'
        type: environment
        required: true

jobs:
  terraform:
    name: "Deploy"
    uses: MohsinNaseem/terraform-workflow/.github/workflows/terraform-stable.yml@main
    with:
      environment: ${{ github.event.inputs.environment }}
      main-repo: ${{ github.event.repository.name }}
    secrets: inherit
```

### Destroy infrastructure
```yaml
name: Terraform destroy

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to run destroy against'
        type: environment
        required: true

jobs:
  terraform:
    name: "${{ github.event.inputs.environment }}"
    uses: MohsinNaseem/terraform-workflow/.github/workflows/terraform-destroy-stable.yml@main
    with:
      environment: ${{ github.event.inputs.environment }}
      main-repo: ${{ github.event.repository.name }}
    secrets: inherit
```

### With subfolder
```yaml
uses: MohsinNaseem/terraform-workflow/.github/workflows/terraform-stable.yml@main
with:
  environment: ${{ github.event.inputs.environment }}
  main-repo: ${{ github.event.repository.name }}
  folder: vpc/standard
```

## Protection Rules

- `live` environment is **blocked** from destroy
- All workflows use **OIDC** — no static keys
- Apply job requires **GitHub Environment approval** if configured
- Plan always runs before apply

## Notes

- Runners: `ubuntu-latest` (upgrade to self-hosted when available)
- Terraform version: `1.7.0`
- AWS Region: `us-east-1`
- Account: `148221445737`
