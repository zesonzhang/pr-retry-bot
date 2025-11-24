# PR Retry Bot

Automated bot to periodically check and retry failed PR checks in target repositories.

## Features

-   **Scheduled Runs**: Checks every 15 minutes for failed PRs.
-   **Manual Trigger**: Can be triggered manually from the GitHub Actions tab.
-   **Toggle Control**: Uses the `ENABLE_SCHEDULED_RETRY` repository variable to enable/disable scheduled runs.

## Setup

### 1. 🔑 Create Personal Access Token (PAT)

This token allows the script to read your PRs and trigger re-runs on your behalf.

1.  Go to **https://github.com/settings/personal-access-tokens/new** to create a new fine-grained token.
2.  Give it a **Token name** (e.g., `pr_retry_bot`).
3.  Set an **Expiration** (e.g., 90 days).
4.  Under **Repository access**, select **"Only select repositories"** and choose the repositories you want to monitor (the ones listed in your `TARGET_REPOS` variable).
5.  Under **Permissions** → **Repository permissions**, click "Add permissions" to set:
    -   **Actions**: Read and write ✅ (Required to re-run workflows)
    -   **Pull requests**: Read ✅ (Required to list and check PR status)
    -   **Metadata**: Read ✅ (Automatically included)
6.  Click **"Generate token"** and copy the generated token.

### 2. 🔒 Add Secret to This Repository

1.  Go to this repository's **Settings** tab.
2.  In the left sidebar, click **Secrets and variables** → **Actions**.
3.  Click the **Secrets** tab, then click **New repository secret**.
4.  **Name**: `MY_PAT`
5.  **Value**: Paste your PAT from Step 1.
6.  Click **Add secret**.

### 3. 🎛️ Create Repository Variables

These variables control the bot's behavior.

1.  Go to this repository's **Settings** tab.
2.  In the left sidebar, click **Secrets and variables** → **Actions**.
3.  Click the **Variables** tab, then click **New repository variable**.

#### Required Variables:

**`MY_USERNAME`**
-   **Value**: Your GitHub username (e.g., `zesonzhang`)
-   **Purpose**: Identifies which PRs to monitor and retry

**`TARGET_REPOS`**
-   **Value**: JSON array of repositories to monitor (e.g., `["openthread/ot-br-posix", "openthread/openthread"]`)
-   **Purpose**: Specifies which repositories to check for failed PRs
-   **Note**: This variable is required for the workflow to run
-   **Important**: If using a fine-grained token, make sure these repositories are included in the token's repository access

**`ENABLE_SCHEDULED_RETRY`**
-   **Value**: `true` (to enable) or `false` (to disable)
-   **Purpose**: Acts as a "master switch" for the 15-minute scheduled runs

**Note**: If `ENABLE_SCHEDULED_RETRY` is set to `false` or does not exist, the scheduled runs will be disabled. Manual runs via the Actions tab will **always** work regardless of this variable's state.

## Configuration

Edit `.github/workflows/retry-failed-prs.yml` to customize:

-   **`schedule.cron`**: Frequency of checks (default: every 15 minutes)

Set repository variables to configure:

-   `MY_USERNAME`: Your GitHub username (required)
-   `TARGET_REPOS`: JSON array of repositories to monitor (required)
-   `ENABLE_SCHEDULED_RETRY`: Enable/disable scheduled runs (`true` or `false`)

### Adding or Changing Repositories

To monitor different repositories, update the `TARGET_REPOS` repository variable:

1.  Go to **Settings** → **Secrets and variables** → **Actions** → **Variables** tab
2.  Edit the `TARGET_REPOS` variable
3.  Set the value as a **JSON array**, for example:
    ```json
    ["openthread/ot-br-posix", "openthread/openthread", "openthread/ot-commissioner"]
    ```

**Important**: The value must be valid JSON format with double quotes around repository names.

## Usage

### ▶️ Manual Run

1.  Go to the **Actions** tab of this repository.
2.  Select the **"Retry Failed OpenThread PRs"** workflow from the list.
3.  Click the **"Run workflow"** dropdown button and then the green **"Run workflow"** button.

###  toggling Scheduled Runs

-   **Enable**: Go to **Settings** → **Variables** → **Actions** and set the `ENABLE_SCHEDULED_RETRY` variable to `true`.
-   **Disable**: Set the `ENABLE_SCHEDULED_RETRY` variable to `false` or delete the variable.

## How It Works

1.  The workflow is triggered either by the schedule or a manual dispatch.
2.  It first checks if the run was manual OR if the `ENABLE_SCHEDULED_RETRY` variable is set to `true`. If neither is true, the job is skipped.
3.  It uses the `gh` CLI (authenticated with `MY_PAT`) to query the `TARGET_REPO` for open PRs by `MY_USERNAME`.
4.  It filters this list down to only PRs that have failed status checks.
5.  For each failed PR, it finds all corresponding failed workflow runs.
6.  It then triggers a **re-run of only the failed jobs** for each of those runs, ensuring all failures are addressed.
7.  All actions are logged in the workflow run.

## License

MIT
