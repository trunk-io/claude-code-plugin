---
name: trunk-ci-setup
description: Best practices for configuring CI pipelines to upload test results to Trunk. Use when editing CI configuration files, setting up test reporting, or configuring JUnit XML output.
---

# Trunk CI Setup Best Practices

When helping users configure CI pipelines or test reporting, follow these guidelines to ensure test results upload correctly to Trunk.

## JUnit XML Output

Trunk ingests test results via **JUnit XML** format. Most test frameworks support this natively or via a plugin:

- **Jest:** Use `jest-junit` reporter → `npm install --save-dev jest-junit`, set `JEST_JUNIT_OUTPUT_DIR`
- **Vitest:** Built-in → `reporters: ['junit']` in `vitest.config.ts`, set `outputFile`
- **pytest:** Built-in → `--junitxml=report.xml` flag
- **Go:** Use `gotestsum` → `gotestsum --junitfile report.xml`
- **JUnit/Gradle:** Built-in → results in `build/test-results/test/`
- **JUnit/Maven:** Built-in → results in `target/surefire-reports/`
- **RSpec:** Use `rspec_junit_formatter` gem
- **PHPUnit:** Built-in → `--log-junit report.xml` flag

Always configure the output path explicitly rather than relying on defaults — this makes the upload step reliable.

## Trunk Analytics Uploader

After tests run, upload results to Trunk.

### GitHub Actions (recommended)

Use the official `trunk-io/analytics-uploader` action. It downloads the CLI for you, so there's no binary URL to keep current:

```yaml
- name: Upload test results to Trunk
  if: ${{ !cancelled() }}   # upload even when tests fail — critical for flaky data
  continue-on-error: true   # an upload hiccup shouldn't fail the build
  uses: trunk-io/analytics-uploader@v1
  with:
    junit-paths: path/to/reports/**/*.xml
    org-slug: <your-org>
    token: ${{ secrets.TRUNK_TOKEN }}
```

### Other CI providers (manual CLI)

Where the action isn't available, download the CLI from the GitHub releases page and run `upload`:

```bash
# Install (one-time, add to CI). Pick the asset for your runner's platform:
#   Linux x86_64:  trunk-analytics-cli-x86_64-unknown-linux.tar.gz
#   Linux arm64:   trunk-analytics-cli-aarch64-unknown-linux.tar.gz
#   macOS arm64:   trunk-analytics-cli-aarch64-apple-darwin.tar.gz
#   macOS x86_64:  trunk-analytics-cli-x86_64-apple-darwin.tar.gz
curl -fsSL https://github.com/trunk-io/analytics-cli/releases/latest/download/trunk-analytics-cli-x86_64-unknown-linux.tar.gz | tar xz

# Upload
./trunk-analytics-cli upload \
  --org-url-slug <your-org> \
  --token $TRUNK_TOKEN \
  --junit-paths "path/to/reports/**/*.xml"
```

The `TRUNK_TOKEN` should be stored as a CI secret, never hardcoded. Use the organization token, not a repo token.

## CI Provider Tips

### GitHub Actions
- Prefer the `trunk-io/analytics-uploader@v1` action (see above) over a hand-rolled CLI download — it stays current with releases.
- Use `if: ${{ !cancelled() }}` (or `if: always()`) on the upload step so results are uploaded even when tests fail — this is critical for capturing flaky test data.
- The test and upload steps should be in the same job to share the filesystem.

### CircleCI
- Use `when: always` on the upload step.
- Store test results with `store_test_results` as well for CircleCI's built-in reporting.

### GitLab CI
- Add the upload to an `after_script` block or use `when: always` on the upload job.
- Use `artifacts:reports:junit` for GitLab's native reporting alongside Trunk uploads.

### Jenkins
- Add the upload as a `post { always { } }` block in the pipeline.
- Use the JUnit plugin for Jenkins-native reporting alongside Trunk.

## Common Mistakes

- **Not uploading on test failure:** If the upload step only runs when tests pass, you'll never capture flaky test data. Always use `if: always()` / `when: always`.
- **Wrong glob pattern:** Double-check the JUnit XML path matches what the test framework actually produces. Use `ls` or `find` in CI to verify before the upload step.
- **Missing token:** The `TRUNK_TOKEN` must be set as a CI secret. Check the Trunk dashboard at https://app.trunk.io for your org's token.
- **Multiple XML files:** Some frameworks produce one XML per test suite. Use glob patterns (`**/*.xml`) to capture all of them.

## Documentation

- GitHub Actions setup: https://docs.trunk.io/flaky-tests/get-started/ci-providers/github-actions
- Analytics CLI reference: https://docs.trunk.io/flaky-tests/reference/cli-reference
- Set up test uploads (MCP): https://docs.trunk.io/flaky-tests/reference/mcp-reference/set-up-test-uploads
- Flaky Tests overview: https://docs.trunk.io/flaky-tests/overview
