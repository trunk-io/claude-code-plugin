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

After tests run, upload results using the `trunk-analytics-cli`:

```bash
# Install (one-time, add to CI)
curl -fsSL https://trunk.io/releases/analytics-cli/latest/trunk-analytics-cli-linux-x86_64.tar.gz | tar xz

# Upload
trunk-analytics-cli upload \
  --org-url-slug <your-org> \
  --token $TRUNK_TOKEN \
  --junit-paths "path/to/reports/**/*.xml"
```

The `TRUNK_TOKEN` should be stored as a CI secret, never hardcoded.

## CI Provider Tips

### GitHub Actions
- Use `if: always()` on the upload step so results are uploaded even when tests fail — this is critical for capturing flaky test data.
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

- Full setup guide: https://docs.trunk.io/flaky-tests/use-mcp-server/mcp-tool-reference/set-up-test-uploads
- Flaky Tests overview: https://docs.trunk.io/flaky-tests
- CI configuration examples: https://docs.trunk.io/flaky-tests
