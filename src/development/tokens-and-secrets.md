---
connie-publish: true
---
# Tokens and Secrets Reference

This guide provides a comprehensive reference for all tokens, secrets, and API credentials required for the markdown-confluence project in various contexts: local development, testing, and CI/CD pipelines.

## Quick Reference Table

| Token Name | Used In | Required For | Where to Get It |
|------------|---------|--------------|-----------------|
| `CONFLUENCE_BASE_URL` | Local dev, PR checks | Confluence connection | Your Confluence URL |
| `ATLASSIAN_USERNAME` | Local dev, PR checks | Confluence auth | Your Atlassian email |
| `ATLASSIAN_API_TOKEN` | Local dev, PR checks, production | Confluence auth | [Atlassian API Tokens](https://id.atlassian.com/manage-profile/security/api-tokens) |
| `CONFLUENCE_PARENT_ID` | Local dev, production | Publishing location | Confluence page ID |
| `CONFLUENCE_CONFIG_FILE` | Optional | Custom config path | File path |
| `RELEASE_PLEASE_PAT` | Release workflow | Publishing releases | [GitHub PAT](https://github.com/settings/tokens) |
| `NODE_AUTH_TOKEN` | Release workflow | NPM publishing | [NPM Access Tokens](https://www.npmjs.com/settings/~/tokens) |
| `SNYK_TOKEN` | Security scanning | Vulnerability checks | [Snyk Account](https://snyk.io/account/) |
| `SCORECARD_TOKEN_PAT` | Security scoring | OSSF Scorecard | [GitHub PAT](https://github.com/settings/tokens) |

## For Local Development & Testing

These credentials are required when developing the project locally and running tests.

### Confluence/Atlassian Credentials

#### `CONFLUENCE_BASE_URL`
- **Description**: Your Confluence instance URL
- **Example**: `https://yourcompany.atlassian.net`
- **Format**: Include `https://` protocol, no trailing slash
- **Required**: Yes (for testing and publishing)

#### `ATLASSIAN_USERNAME`
- **Description**: Your Atlassian account email address
- **Example**: `yourname@example.com`
- **Required**: Yes (for authentication)

#### `ATLASSIAN_API_TOKEN`
- **Description**: Atlassian API token for authentication
- **How to create**:
  1. Go to [Atlassian API Tokens](https://id.atlassian.com/manage-profile/security/api-tokens)
  2. Click "Create API token"
  3. Give it a label (e.g., "markdown-confluence-dev")
  4. Copy the token immediately (you won't be able to see it again)
- **Security**: Keep this secret! Never commit it to version control
- **Required**: Yes (for API authentication)

#### `CONFLUENCE_PARENT_ID`
- **Description**: The Confluence page ID where content will be published as child pages
- **How to find**:
  1. Navigate to the parent page in Confluence
  2. Look at the URL: `https://yoursite.atlassian.net/wiki/spaces/SPACE/pages/123456/Page+Title`
  3. The number `123456` is your page ID
- **Required**: Yes (for publishing)

#### `CONFLUENCE_CONFIG_FILE` (Optional)
- **Description**: Path to a custom configuration file
- **Default**: `.markdown-confluence.json` in the current directory
- **Required**: No

### Setting Up Environment Variables

#### macOS / Linux
```bash
export CONFLUENCE_BASE_URL="https://yourcompany.atlassian.net"
export ATLASSIAN_USERNAME="your-email@example.com"
export ATLASSIAN_API_TOKEN="your-api-token-here"
export CONFLUENCE_PARENT_ID="123456"
```

#### Windows (Command Prompt)
```cmd
set CONFLUENCE_BASE_URL="https://yourcompany.atlassian.net"
set ATLASSIAN_USERNAME="your-email@example.com"
set ATLASSIAN_API_TOKEN="your-api-token-here"
set CONFLUENCE_PARENT_ID="123456"
```

#### Windows (PowerShell)
```powershell
$env:CONFLUENCE_BASE_URL="https://yourcompany.atlassian.net"
$env:ATLASSIAN_USERNAME="your-email@example.com"
$env:ATLASSIAN_API_TOKEN="your-api-token-here"
$env:CONFLUENCE_PARENT_ID="123456"
```

### Using a Configuration File

Create a `.markdown-confluence.json` file in your project root:

```json
{
  "confluenceBaseUrl": "https://yourcompany.atlassian.net",
  "confluenceParentId": "123456",
  "atlassianUserName": "your-email@example.com",
  "folderToPublish": "docs",
  "contentRoot": "/docs"
}
```

**Note**: Store sensitive values like `atlassianApiToken` in environment variables, not in the config file.

## For GitHub Actions Workflows

These secrets are required for the various GitHub Actions workflows in the project's CI/CD pipeline.

### Release & Publishing Tokens

Used in: `.github/workflows/release-please.yml`

#### `RELEASE_PLEASE_PAT`
- **Description**: GitHub Personal Access Token for release automation
- **Required Permissions**:
  - `repo` (Full control of private repositories)
  - `write:packages` (Upload packages to GitHub Package Registry)
  - `workflow` (Update GitHub Action workflows)
- **How to create**:
  1. Go to [GitHub Settings > Developer settings > Personal access tokens > Tokens (classic)](https://github.com/settings/tokens)
  2. Click "Generate new token (classic)"
  3. Give it a descriptive name (e.g., "markdown-confluence-releases")
  4. Select the required scopes listed above
  5. Click "Generate token"
  6. Copy the token immediately
- **Used for**:
  - Creating releases
  - Creating/updating pull requests
  - Publishing Docker images to GitHub Container Registry
  - Accessing the obsidian-integration repository

#### `NODE_AUTH_TOKEN`
- **Description**: NPM access token for publishing packages to npm registry
- **How to create**:
  1. Log in to [npmjs.com](https://www.npmjs.com/)
  2. Click on your profile picture > "Access Tokens"
  3. Click "Generate New Token" > "Classic Token"
  4. Select "Automation" type
  5. Copy the token
- **Used for**: Publishing `@markdown-confluence/*` packages to npm

### Security Scanning Tokens

#### `SNYK_TOKEN`
Used in: `.github/workflows/snyk.yml`

- **Description**: Snyk API token for vulnerability scanning
- **How to create**:
  1. Sign up/log in at [snyk.io](https://snyk.io/)
  2. Go to Account Settings
  3. Find your API token or generate a new one
  4. Copy the token
- **Used for**:
  - Scanning dependencies for vulnerabilities
  - Scanning code for security issues
  - Scanning Docker images
  - Generating SBOMs (Software Bill of Materials)

#### `SCORECARD_TOKEN_PAT`
Used in: `.github/workflows/scorecard.yml`

- **Description**: GitHub PAT for OSSF Scorecard security analysis
- **Required Permissions**:
  - `repo` (for public repositories)
  - `public_repo` (minimum for public repos)
- **How to create**: Same process as `RELEASE_PLEASE_PAT` but with minimal permissions
- **Used for**: Running OpenSSF Scorecard security checks

### Testing Tokens

Used in: `.github/workflows/pr-check.yml`

For CI/CD testing, you'll need the same Confluence credentials as local development:
- `CONFLUENCE_BASE_URL`
- `ATLASSIAN_USERNAME`
- `ATLASSIAN_API_TOKEN`

**Recommendation**: Create a separate test Confluence space and API token specifically for CI/CD to avoid polluting your production space during testing.

## Adding Secrets to GitHub

To add these secrets to your GitHub repository:

1. Navigate to your repository on GitHub
2. Click on **Settings** tab
3. In the left sidebar, click **Secrets and variables** > **Actions**
4. Click **New repository secret**
5. Enter the secret name (exactly as shown in the table above)
6. Paste the secret value
7. Click **Add secret**

Repeat this process for each required secret.

## Security Best Practices

### Do's ✅
- Store all sensitive tokens as secrets (environment variables or GitHub secrets)
- Use separate tokens for development, testing, and production
- Rotate tokens periodically
- Use tokens with minimal required permissions
- Revoke tokens immediately if compromised
- Use different Confluence spaces for testing vs. production

### Don'ts ❌
- Never commit tokens to version control
- Never share tokens in chat, email, or documentation
- Don't use production credentials for testing
- Don't grant more permissions than necessary
- Don't reuse tokens across multiple projects
- Don't store tokens in configuration files that are committed to git

## Troubleshooting

### "Unauthorized" or "401" errors
- Verify your `ATLASSIAN_API_TOKEN` is correct
- Check that your `ATLASSIAN_USERNAME` matches the account that created the token
- Ensure the token hasn't expired or been revoked

### "Not Found" or "404" errors
- Verify your `CONFLUENCE_BASE_URL` is correct
- Check that `CONFLUENCE_PARENT_ID` exists and you have access to it
- Ensure your account has permissions to view/edit the parent page

### GitHub Actions failing with secret errors
- Verify all required secrets are added to the repository
- Check that secret names match exactly (case-sensitive)
- Ensure PATs have the required permissions

### Docker build failures in CI
- Check that `ghcr.io:443` is in the allowed endpoints for Harden Runner
- Verify the Docker image reference is correct in the Dockerfile

## Additional Resources

- [Atlassian API Tokens Documentation](https://support.atlassian.com/atlassian-account/docs/manage-api-tokens-for-your-atlassian-account/)
- [GitHub Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- [NPM Access Tokens](https://docs.npmjs.com/about-access-tokens)
- [Snyk Documentation](https://docs.snyk.io/)
- [OSSF Scorecard](https://github.com/ossf/scorecard)