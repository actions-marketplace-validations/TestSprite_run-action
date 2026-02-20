# TestSprite GitHub Action

Trigger TestSprite E2E tests from GitHub Actions, and optionally post test results on PRs and block PRs when tests do not pass.

## Usage

Add the action to your workflow file:

```yaml
name: TestSprite E2E Tests
on:
  push:
    branches:
      - main
  pull_request:

jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: TestSprite/run-action@v1
        with:
          testsprite-api-key: ${{ secrets.TESTSPRITE_API_KEY }}
          base_url: 'https://your-app.example.com'
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Input                | Description                                                                                 | Required | Default |
| -------------------- | ------------------------------------------------------------------------------------------- | -------- | ------- |
| `testsprite-api-key` | TestSprite API Key for authentication                                                       | Yes      | -       |
| `base_url`           | Base URL of the site to run E2E tests against                                               | Yes      | -       |
| `github-token`       | GitHub token used to post test report as a PR comment                                       | No       | -       |
| `blocking`           | When `true`, fails the step and blocks the PR if any test fails                             | No       | `false` |
| `priority`           | Priorities to run, comma-separated (e.g. `High,Medium,Low`). Omit or leave empty to run all | No       | -       |

You can find your API key in your [TestSprite project settings](https://app.testsprite.com).

## Outputs

| Output         | Description                           |
| -------------- | ------------------------------------- |
| `total_tests`  | Total number of tests that ran        |
| `passed_tests` | Number of tests that passed           |
| `result_url`   | URL to view the detailed test results |

## PR Comments

When `github-token` is provided, the action automatically posts a test results summary as a comment on the pull request. This gives your team instant visibility into test outcomes without leaving GitHub.

```yaml
- uses: TestSprite/run-action@v1
  with:
    testsprite-api-key: ${{ secrets.TESTSPRITE_API_KEY }}
    base_url: 'https://your-app.example.com'
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Blocking Mode

By default the action triggers tests and exits immediately. Enable blocking mode to have the action wait for the test run to complete and fail the step if tests don't pass — useful for enforcing quality gates on PRs.

```yaml
- uses: TestSprite/run-action@v1
  with:
    testsprite-api-key: ${{ secrets.TESTSPRITE_API_KEY }}
    base_url: 'https://your-app.example.com'
    github-token: ${{ secrets.GITHUB_TOKEN }}
    blocking: 'true'
```

When blocking is enabled, the action will:

1. Trigger the test run
2. Poll until the run completes
3. Post the results as a PR comment (if `github-token` is set)
4. Fail the GitHub Actions step if any tests fail

## Running by Priority

Use the `priority` input to run only a subset of tests based on their priority level. This is useful for running only critical tests on every push and reserving full runs for scheduled pipelines.

```yaml
# Run only High and Medium priority tests
- uses: TestSprite/run-action@v1
  with:
    testsprite-api-key: ${{ secrets.TESTSPRITE_API_KEY }}
    base_url: 'https://your-app.example.com'
    priority: 'High,Medium'
```

Accepted values: `High`, `Medium`, `Low`. Omit the input or leave it empty to run all tests regardless of priority.

## Preview Deployments

To run tests against a preview deployment (e.g. from Vercel or Netlify), pass the preview URL as `base_url`. Combine this with `blocking: true` to gate merges on a passing test run against the exact build being reviewed.

```yaml
name: TestSprite E2E Tests
on:
  pull_request:

jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - name: Wait for preview deployment
        # Add your deployment step here and capture the URL

      - uses: TestSprite/run-action@v1
        with:
          testsprite-api-key: ${{ secrets.TESTSPRITE_API_KEY }}
          base_url: 'https://preview-123.vercel.app'
          github-token: ${{ secrets.GITHUB_TOKEN }}
          blocking: 'true'
```

## Using Outputs

You can reference the action's outputs in subsequent steps:

```yaml
- uses: TestSprite/run-action@v1
  id: testsprite
  with:
    testsprite-api-key: ${{ secrets.TESTSPRITE_API_KEY }}
    base_url: 'https://your-app.example.com'
    blocking: 'true'

- name: Print results
  run: |
    echo "Passed: ${{ steps.testsprite.outputs.passed_tests }} / ${{ steps.testsprite.outputs.total_tests }}"
    echo "Results: ${{ steps.testsprite.outputs.result_url }}"
```

## Development

```bash
# Install dependencies
npm install

# Build
npm run package

# Format and lint
npm run all
```

## License

MIT
