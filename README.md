# TestSprite GitHub Action

Trigger TestSprite E2E tests from GitHub Actions, and optionally post test
results on PRs and block PRs when tests do not pass.

## Usage

Reference this action in your workflow:

```yaml
- name: TestSprite E2E Tests
  uses: TestSprite/run-action@v1
  with:
    testsprite-api-key: ${{ secrets.TESTSPRITE_API_KEY }}
    base_url: "https://your-app.example.com"
    # optional
    github-token: ${{ secrets.GITHUB_TOKEN }}
    blocking: "false"
    priority: "High,Medium"
```

### Inputs

| Input                | Required | Description                                                                                |
| -------------------- | -------- | ------------------------------------------------------------------------------------------ |
| `testsprite-api-key` | Yes      | TestSprite API Key for authentication                                                      |
| `base_url`           | Yes      | Base URL of the site to run E2E tests against                                              |
| `github-token`       | No       | GitHub token (defaults to GITHUB_TOKEN if not set), used to post test report in PR comment |
| `blocking`           | No       | When set to `true`, step fails and blocks PR if any test fails. Default: `false`           |
| `priority`           | No       | Priorities to run, comma-separated e.g. High,Medium,Low; omit or empty to run all          |

### Outputs

| Output         | Description                           |
| -------------- | ------------------------------------- |
| `total_tests`  | Total number of tests that ran        |
| `passed_tests` | Number of tests that passed           |
| `result_url`   | URL to view the detailed test results |
