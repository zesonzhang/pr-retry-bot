# PR Retry Bot

Automated bot to periodically check and retry failed PR checks in target repositories.

## Features

-   **Scheduled Runs**: Checks every 15 minutes for failed PRs.
-   **Manual Trigger**: Can be triggered manually from the GitHub Actions tab.
-   **Toggle Control**: Uses the `ENABLE_SCHEDULED_RETRY` repository variable to enable/disable scheduled runs.

## Setup

### 1. 🔑 Create Personal Access Token (PAT)

This token allows the script to read your PRs and trigger re-runs on your behalf.

1.  Go to **GitHub Settings** → **Developer settings** → **Personal access tokens** → **Tokens (classic)**.
2.  Click **"Generate new token (classic)"**.
3.  Give it a **Note** (e.g., `pr_retry_bot`).
4.  Set an **Expiration** (e.g., 90 days).
5.  Select scopes:
    -   ✅ `repo` (Required to read PR/action status and trigger action re-runs)
6.  Click **"Generate token"** and copy the generated token.

### 2. 🔒 Add Secret to This Repository

1.  Go to this repository's **Settings** tab.
2.  In the left sidebar, click **Secrets and variables** → **Actions**.
3.  Click the **Secrets** tab, then click **New repository secret**.
4.  **Name**: `MY_PAT`
5.  **Value**: Paste your PAT from Step 1.
6.  Click **Add secret**.

### 3. 🎛️ Create Repository Variable (The Switch)

This variable acts as a "master switch" for the 15-minute scheduled runs.

1.  Go to this repository's **Settings** tab.
2.  In the left sidebar, click **Secrets and variables** → **Actions**.
3.  Click the **Variables** tab, then click **New repository variable**.
4.  **Name**: `ENABLE_SCHEDULED_RETRY`
5.  **Value**: `true` (to enable) or `false` (to disable).
6.  Click **Add variable**.

**Note**: If this variable is set to `false` or does not exist, the scheduled runs will be disabled. Manual runs via the Actions tab will **always** work regardless of this variable's state.

## Configuration

Edit `.github/workflows/retry-failed-prs.yml` to customize:

-   `MY_USERNAME`: Your GitHub username (default: `zesonzhang`)
-   `TARGET_REPO`: The repository to monitor (default: `openthread/ot-br-posix`)
-   `schedule.cron`: Frequency of checks (default: every 15 minutes)

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
5.  For each failed PR, it finds the corresponding failed workflow `run_id`.
6.  It then triggers a **re-run of only the failed jobs** for that `run_id`.
7.  All actions are logged in the workflow run.

## License

MIT
