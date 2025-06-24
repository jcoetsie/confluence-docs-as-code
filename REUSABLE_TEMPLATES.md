# Reusable Azure DevOps Templates for Confluence Documentation

This repository provides reusable Azure DevOps pipeline templates that function similarly to GitHub Actions, allowing you to easily integrate Confluence documentation publishing into any Azure DevOps project.

## Template Types

### 1. Full Job Template (`templates/confluence-docs-template.yml`)

A complete pipeline template that includes build, test, and publish jobs. Use this when you want a turnkey solution.

### 2. Step Template (`templates/confluence-publish-steps.yml`)

A step template that only handles the Confluence publishing. Use this to integrate into existing pipelines.

## Quick Start

### Option 1: Using the Full Template (Recommended for new projects)

Create an `azure-pipelines.yml` in your repository:

```yaml
trigger:
  branches:
    include:
    - main

pool:
  vmImage: 'ubuntu-latest'

variables:
- group: confluence-credentials

extends:
  template: templates/confluence-docs-template.yml
  parameters:
    confluence_tenant: $(CONFLUENCE_TENANT)
    confluence_space: $(CONFLUENCE_SPACE)
    confluence_user: $(CONFLUENCE_USER)
    confluence_token: $(CONFLUENCE_TOKEN)
    confluence_parent_page: 'Documentation'
    confluence_title_prefix: '[MyProject] '
```

### Option 2: Using the Step Template (For existing pipelines)

Add to your existing pipeline:

```yaml
- job: PublishDocs
  steps:
  - template: templates/confluence-publish-steps.yml
    parameters:
      confluence_tenant: $(CONFLUENCE_TENANT)
      confluence_space: $(CONFLUENCE_SPACE)
      confluence_user: $(CONFLUENCE_USER)
      confluence_token: $(CONFLUENCE_TOKEN)
```

### Option 3: Using from External Repository

Reference the template from another repository:

```yaml
resources:
  repositories:
  - repository: confluence-templates
    type: git
    name: YourOrg/confluence-docs-as-code

extends:
  template: templates/confluence-docs-template.yml@confluence-templates
  parameters:
    # ... your parameters
```

## Template Parameters

### Required Parameters

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `confluence_tenant` | string | Your Atlassian tenant name | `mycompany` |
| `confluence_space` | string | Confluence space key | `DOCS` |
| `confluence_user` | string | Confluence username | `john.doe@company.com` |
| `confluence_token` | string | Confluence API token | `$(CONFLUENCE_TOKEN)` |

### Optional Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `confluence_parent_page` | string | `''` | Parent page title |
| `confluence_title_prefix` | string | `''` | Prefix for all page titles |
| `confluence_force_update` | string | `'no'` | Force update all pages (`yes`/`no`) |
| `confluence_cleanup` | string | `'no'` | Delete all pages (`yes`/`no`) |
| `kroki_enabled` | string | `'yes'` | Enable Kroki diagrams (`yes`/`no`) |
| `kroki_host` | string | `'https://kroki.io'` | Kroki server URL |
| `mermaid_renderer` | string | `'kroki'` | Mermaid renderer (`none`/`kroki`/`mermaid-plugin`) |
| `plantuml_renderer` | string | `'kroki'` | PlantUML renderer (`none`/`kroki`/`plantuml`) |
| `node_versions` | array | `['16.x', '18.x']` | Node.js versions to test against |
| `publish_node_version` | string | `'18.x'` | Node.js version for publishing |
| `working_directory` | string | `'.'` | Working directory path |
| `trigger_branches` | array | `['main', 'master']` | Branches that trigger publishing |

## Setup Instructions

### 1. Set Up Variable Groups

Create a variable group named `confluence-credentials` with these variables:

- `CONFLUENCE_TENANT` - Your Atlassian tenant
- `CONFLUENCE_SPACE` - Your space key
- `CONFLUENCE_USER` - Your username
- `CONFLUENCE_TOKEN` - Your API token (mark as secret)

Optional variables:

- `CONFLUENCE_PARENT_PAGE`
- `CONFLUENCE_TITLE_PREFIX`

### 2. Create Your Pipeline

Choose one of the usage examples from the `examples/` folder and customize it for your needs.

### 3. Configure Repository Access (for external usage)

If using from another repository:

1. Ensure the template repository is accessible
2. Use the correct repository reference format
3. Consider using specific tags/branches for stability

## Examples

See the `examples/` folder for complete usage examples:

- `azure-pipelines-template-usage.yml` - Using the full template
- `azure-pipelines-custom-usage.yml` - Using step template in custom pipeline
- `azure-pipelines-external-usage.yml` - Using template from external repository

## Advanced Configuration

### Environment-Specific Deployments

```yaml
# Deploy to different spaces based on branch
extends:
  template: templates/confluence-docs-template.yml
  parameters:
    confluence_space: ${{ if eq(variables['Build.SourceBranch'], 'refs/heads/main') }}:
      PROD_DOCS
    ${{ else }}:
      DEV_DOCS
    confluence_title_prefix: ${{ if eq(variables['Build.SourceBranch'], 'refs/heads/main') }}:
      '[PROD] '
    ${{ else }}:
      '[DEV] '
```

### Multiple Node.js Versions

```yaml
extends:
  template: templates/confluence-docs-template.yml
  parameters:
    node_versions:
    - '16.x'
    - '18.x'
    - '20.x'
    publish_node_version: '20.x'
```

### Custom Working Directory

```yaml
extends:
  template: templates/confluence-docs-template.yml
  parameters:
    working_directory: './documentation'
```

## Security Best Practices

1. **Always use variable groups** for credentials
2. **Mark sensitive variables as secret** (especially `CONFLUENCE_TOKEN`)
3. **Use specific template versions** in production (tag references)
4. **Limit template repository access** to authorized users
5. **Review template changes** before updating references

## Troubleshooting

### Common Issues

1. **Template not found**: Check repository reference and path
2. **Parameter validation errors**: Ensure all required parameters are provided
3. **Authentication failures**: Verify credentials in variable group
4. **Build failures**: Check Node.js version compatibility

### Debug Tips

1. Enable system diagnostics: `System.Debug: true`
2. Check variable values in pipeline logs
3. Verify template parameter syntax
4. Test with minimal parameter set first

## Contributing

When updating templates:

1. Test changes thoroughly
2. Update parameter documentation
3. Create new examples if needed
4. Consider backward compatibility
5. Tag stable releases

## Migration from GitHub Actions

| GitHub Actions | Azure DevOps Templates |
|----------------|------------------------|
| `uses: your-action@v1` | `template: templates/template.yml` |
| `with:` | `parameters:` |
| `secrets:` | Variable groups |
| `${{ secrets.TOKEN }}` | `$(TOKEN)` |

The templates provide the same functionality as the original GitHub Action while leveraging Azure DevOps-specific features for better integration and security.
