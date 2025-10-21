# PR Retry Bot

Automated bot to periodically check and retry failed PR checks in target repositories.

## Features

- **Scheduled Runs**: Checks every 15 minutes for failed PRs
- **Manual Trigger**: Can be triggered manually from GitHub Actions tab
- **Toggle Control**: Uses `ENABLE_SCHEDULED_RETRY` variable to enable/disable scheduled runs

## Setup

### 1. Create Personal Access Token (PAT)

1. Go to [GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)](https://github.com/settings/tokens)
2. Click "Generate new token (classic)"
3. Select scopes:
   - ✅ `repo` (full control of private repositories)
   - ✅ `workflow` (update GitHub Actions workflows)
4. Copy the generated token

### 2. Add Secret to This Repository

1. Go to [this repository's Settings → Secrets and variables → Actions](https://github.com/zesonzhang/pr-retry-bot/settings/secrets/actions)
2. Click "New repository secret"
3. Name: `MY_PAT`
4. Value: Paste your PAT
5. Click "Add secret"

### 3. Create Repository Variable (Optional)

To enable scheduled runs:

1. Go to [Settings → Secrets and variables → Actions → Variables tab](https://github.com/zesonzhang/pr-retry-bot/settings/variables/actions)
2. Click "New repository variable"
3. Name: `ENABLE_SCHEDULED_RETRY`
4. Value: `true`
5. Click "Add variable"

**Note**: If you don't create this variable, scheduled runs will be disabled by default. You can still trigger manually.

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
