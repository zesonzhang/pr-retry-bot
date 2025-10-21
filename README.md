# PR Retry Bot

Automated bot to periodically check and retry failed PR checks in target repositories.

## Features

- **Scheduled Runs**: Checks every 15 minutes for failed PRs
- **Manual Trigger**: Can be triggered manually from GitHub Actions tab
- **Toggle Control**: Uses `ENABLE_SCHEDULED_RETRY` variable to enable/disable scheduled runs

## Setup

### 1. Create Personal Access Token (PAT)

This token allows the script to read your PRs and trigger re-runs on your behalf.

1. Go to [GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)](https://github.com/settings/tokens)
2. Click "Generate new token (classic)"
3. Give it a **Note** (e.g., `pr_retry_bot`)
4. Set an **Expiration**
5. Select scopes:
   - ✅ `repo` (Required to read PR/action status and trigger action re-runs)
6. Copy the generated token

### 2. Add Secret to This Repository

1. Go to [this repository's Settings](https://github.com/zesonzhang/pr-retry-bot/settings) tab
2. In the left sidebar, click **Secrets and variables** → **Actions**
3. Click the **Secrets** tab, then click **New repository secret**
4. **Name**: `MY_PAT`
5. **Value**: Paste your PAT from Step 1
6. Click **Add secret**

### 3. Create Repository Variable (Optional Switch)

This variable acts as a "master switch" for the 15-minute scheduled runs.

1. Go to [this repository's Settings](https://github.com/zesonzhang/pr-retry-bot/settings) tab
2. In the left sidebar, click **Secrets and variables** → **Actions**
3. Click the **Variables** tab, then click **New repository variable**
4. **Name**: `ENABLE_SCHEDULED_RETRY`
5. **Value**: `true`
6. Click **Add variable**

**Note**: If this variable is set to `false` or does not exist, the scheduled runs will be disabled by default. Manual runs via the Actions tab will always work.

## Configuration

Edit `.github/workflows/retry-failed-prs.yml` to customize:

- `MY_USERNAME`: Your GitHub username (default: `zesonzhang`)
- `TARGET_REPO`: The repository to monitor (default: `openthread/ot-br-posix`)
- `schedule.cron`: Frequency of checks (default: every 15 minutes)

## Usage

### Manual Run
1. Go to [Actions tab](https://github.com/zesonzhang/pr-retry-bot/actions)
2. Select "Retry Failed OpenThread PRs" workflow
3. Click "Run workflow"

### Enable/Disable Scheduled Runs
- **Enable**: Set `ENABLE_SCHEDULED_RETRY` variable to `true`
- **Disable**: Set it to `false` or delete the variable

## How It Works

1. Queries the target repository for open PRs by the specified author
2. Filters PRs with failed status checks
3. Finds the failed workflow run ID
4. Triggers a re-run of only the failed jobs
5. Logs all actions for debugging

## Permissions Required

The PAT needs these permissions on the target repository:
- Read PRs and workflow runs
- Trigger workflow re-runs

## License

MIT
