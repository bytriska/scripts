# @bytriska/scripts

A utility tool designed to synchronize repository secrets across multiple repositories.

This tool streamlines the process of setting a specific secret (such as an NPM token) across many repositories simultaneously. Currently, it is optimized for **NPM Token** synchronization. If you require support for other secret types, please feel free to open an issue.

## Getting Started

### 1. Setup Repository

To use this tool, you can either fork or clone this repository to your own GitHub account.

```bash
# Clone the repository
git clone https://github.com/bytriska/scripts.git

# Or simply click the "Fork" button in the top-right corner of the GitHub UI.

```

### 2. Configuration

Configure the tool by creating or editing the `config.json` file in the root of the workspace. Below is an example configuration:

```json
// config.json
{
  "registryManager": {
    "npm": {
      "secretName": "NPM_TOKEN",
      "policy": {
        "forceUpdateAll": false,
        "verifyAuthenticity": true
      },
      "allowedRepositories": ["my-first-repo", "my-second-repo"]
    }
  }
}

```

#### Configuration Options

| Option | Type | Description |
| --- | --- | --- |
| `secretName` | `string` | The name of the secret key that will be created or updated in the target repositories (e.g., `NPM_TOKEN`). |
| `policy.forceUpdateAll` | `boolean` | If `true`, the secret will be pushed to **every** non-archived repository in your account.<br>

<br>If `false`, it only updates repositories explicitly listed in `allowedRepositories`. |
| `policy.verifyAuthenticity` | `boolean` | If `true`, the workflow attempts to authenticate with the NPM registry using the provided token before syncing. If validation fails, the workflow stops to prevent invalid tokens from being distributed. |
| `allowedRepositories` | `array` | A list of exact repository names to update. This is only used when `forceUpdateAll` is set to `false`. |

### 3. Setting Secrets

Before running the synchronization, you must set the following secrets in the **Settings > Secrets and variables > Actions** menu of *this* repository.

| Secret Name | Description | Required Permissions |
| --- | --- | --- |
| **`PAT_TOKEN`** | A GitHub Personal Access Token (Classic) used to write secrets to your other repositories. | **Scopes:**<br><br>`repo` (Full control of private repositories)<br><br>OR `public_repo` (if syncing only to public repos). |
| **`NEW_NPM_TOKEN`** | The actual Automation or Publish token generated from npmjs.com. | N/A |

## Usage

This tool utilizes a **manual trigger** (`workflow_dispatch`). To sync your secrets:

1. Navigate to the **Actions** tab in this repository.
2. Select the **Sync NPM Credentials** workflow from the sidebar.
3. Click the **Run workflow** dropdown button.
4. Select the branch (usually `main`) and click **Run workflow**.

### Workflow Summary

Once the job completes, check the **Summary** page of the workflow run. It will provide a table showing which repositories were updated and which were skipped based on your configuration.
