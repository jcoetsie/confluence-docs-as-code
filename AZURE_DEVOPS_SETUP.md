# Azure DevOps Pipeline Setup

This repository includes Azure DevOps pipeline configurations that are compatible with the GitHub Action functionality for publishing MkDocs sites to Confluence.

## Pipeline Files

- `azure-pipelines.yml` - Full pipeline with matrix strategy for multiple Node.js versions
- `azure-pipelines-simple.yml` - Simplified pipeline with separate build/test and publish jobs

## Setup Instructions

### 1. Choose Your Pipeline

- Use `azure-pipelines.yml` if you want to test against multiple Node.js versions in parallel
- Use `azure-pipelines-simple.yml` if you prefer a simpler setup with separate jobs

### 2. Set Up Variables

You have two options for managing variables:

#### Option A: Pipeline Variables (azure-pipelines.yml)

Set these variables directly in your Azure DevOps pipeline:

- `CONFLUENCE_TENANT` - Your Atlassian tenant name
- `CONFLUENCE_SPACE` - The key of the space to host your documentation
- `CONFLUENCE_USER` - The username used to authenticate with Confluence REST API
- `CONFLUENCE_TOKEN` - The token used to authenticate with Confluence REST API (mark as secret)
- `CONFLUENCE_PARENT_PAGE` - (Optional) The title of an existing page to use as parent
- `CONFLUENCE_TITLE_PREFIX` - (Optional) This prefix will be prepended to all confluence page titles
- `CONFLUENCE_FORCE_UPDATE` - (Optional) When set to "yes" all pages will be published including unchanged
- `CONFLUENCE_CLEANUP` - (Optional) When set to "yes" all pages will be deleted from confluence
- `KROKI_ENABLED` - (Optional) When "yes" conversion of Mermaid & PlantUML graphs into images
- `KROKI_HOST` - (Optional) Overwrite to use a local kroki deployment
- `MERMAID_RENDERER` - (Optional) The strategy to use for mermaid graphs
- `PLANTUML_RENDERER` - (Optional) The strategy to use for plantUml graphs

#### Option B: Variable Groups (azure-pipelines-simple.yml)

1. In Azure DevOps, go to Pipelines → Library → Variable groups
2. Create a new variable group named `confluence-config`
3. Add the same variables as listed above
4. Mark sensitive variables (like `CONFLUENCE_TOKEN`) as secret

### 3. Create the Pipeline

1. In Azure DevOps, go to Pipelines → Pipelines
2. Click "New pipeline"
3. Select your repository
4. Choose "Existing Azure Pipelines YAML file"
5. Select either `azure-pipelines.yml` or `azure-pipelines-simple.yml`
6. Review and create the pipeline

### 4. Configure Branch Policies (Optional)

To ensure documentation is only published from the main branch:

1. Go to Repos → Branches
2. Click on the three dots next to your main branch
3. Select "Branch policies"
4. Add a build validation policy that requires the pipeline to pass

## Key Differences from GitHub Actions

1. **Triggers**: Uses `trigger` and `pr` instead of `on`
2. **Jobs**: Uses `jobs` and `steps` with different syntax
3. **Environment Variables**: Uses `$(variableName)` syntax
4. **Caching**: Uses Azure DevOps Cache task instead of GitHub's cache action
5. **Node Setup**: Uses NodeTool task instead of setup-node action

## Security Best Practices

1. Always mark sensitive variables (tokens, passwords) as secret
2. Use variable groups for better organization and reusability
3. Consider using Azure Key Vault for highly sensitive secrets
4. Limit pipeline permissions to only what's necessary

## Troubleshooting

- Ensure all required variables are set and not empty
- Check that the `dist/index.js` file exists after the build step
- Verify your Confluence credentials and permissions
- Review pipeline logs for specific error messages

## Migration from GitHub Actions

If you're migrating from GitHub Actions:

1. Copy your existing variable values from GitHub secrets to Azure DevOps variables
2. Update any custom steps to use Azure DevOps syntax
3. Test the pipeline on a feature branch before deploying to main
