# NPM Credential Sync

A centralized utility workflow to automate the rotation and synchronization of NPM authentication tokens across your GitHub repositories. 

Instead of manually updating repository secrets in dozens of projects when an NPM token expires, this repository manages the propagation securely via GitHub Actions.

## Key Features

* **Automated Propagation**: Updates repository secrets across your entire account or specific target repositories.
* **Token Validation**: Automatically verifies that the new NPM token is valid (via `npm whoami`) before attempting to sync.
* **Configurable Scope**: Use `config.json` to switch between updating *all* repositories or a specific allowlist.
* **Detailed Reporting**: Generates a clear deployment summary in the GitHub Actions UI showing which repos were updated or skipped.

## Configuration

The behavior of the sync script is controlled entirely by `config.json`.

```json
{
  "registryManager": {
    "npm": {
      "secretName": "NPM_TOKEN",
      "policy": {
        "forceUpdateAll": false,
        "verifyAuthenticity": true
      },
      "allowedRepositories": [
        "priority-find-up",
        "my-other-lib"
      ]
    }
  }
}
```

### Configuration Options

| Option | Type | Description |
| --- | --- | --- |
| `secretName` | `string` | The name of the secret key to be created/updated in the target repositories (e.g., `NPM_TOKEN`). |
| `policy.forceUpdateAll` | `boolean` | If `true`, the token will be pushed to **every** non-archived repository in your account. If `false`, it only updates repos listed in `allowedRepositories`. |
| `policy.verifyAuthenticity` | `boolean` | If `true`, the workflow attempts to authenticate with the NPM registry using the new token. If validation fails, the workflow stops immediately. |
| `allowedRepositories` | `array` | A list of exact repository names to update when `forceUpdateAll` is set to `false`. |

## Setup & Prerequisites

To use this workflow, you need to configure two secrets in **this** repository (`scripts`).

1. Go to **Settings** > **Secrets and variables** > **Actions**.
2. Add the following repository secrets:

| Secret Name | Description | Required Permissions |
| --- | --- | --- |
| **`PAT_TOKEN`** | A GitHub Personal Access Token used to write secrets to *other* repositories. | **Scopes:** `repo` (Full control of private repositories) or `public_repo` (if using only public). |
| **`NEW_NPM_TOKEN`** | The actual Automation or Publish token generated from npmjs.com. | N/A |

*Note: Fine-grained tokens must have Read/Write access to "Secrets" and "Metadata" on all target repos.* 

## Usage

This workflow is triggered manually via **Workflow Dispatch**.

1. Update your `config.json` file if you need to change the target repositories.
2. Navigate to the **Actions** tab in this repository.
3. Select **Sync NPM Credentials** from the sidebar.
4. Click **Run workflow**.

### Workflow Steps

1. **Checkout**: Pulls the `scripts` repository.
2. **Validate**: (If enabled) Checks if `NEW_NPM_TOKEN` is valid.
3. **Sync**: Iterates through your repositories via GitHub CLI (`gh`).
4. **Summary**: Outputs a table in the job summary:

## Project Structure

```text
scripts/
├── .github/
│   └── workflows/
│       └── sync-npm.yml    # The logic for validation and syncing
├── config.json             # Configuration for policies and allowlists
└── README.md               # Documentation

```

## Security Note

* **Least Privilege:** Ensure your `PAT_TOKEN` is only used in this repository. Do not share it.
* **Traceability:** The workflow logs run initiation by `github.actor` for audit purposes.
* **Logs:** The actual token values are masked by GitHub Actions automatically in the logs.
