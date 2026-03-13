---
description: Set up test result uploads to Trunk for your repository
argument-hint: [ci_provider]
---

# Set Up Trunk Test Uploads

Use the Trunk MCP server to generate a setup plan for uploading test results.

1. If a CI provider is given in the arguments (e.g., "github-actions", "circleci", "jenkins"), use it. Otherwise, detect the CI provider by looking for configuration files in the repo:
   - `.github/workflows/` → GitHub Actions
   - `.circleci/` → CircleCI
   - `Jenkinsfile` → Jenkins
   - `.gitlab-ci.yml` → GitLab CI
   - `bitbucket-pipelines.yml` → Bitbucket Pipelines
   - `azure-pipelines.yml` → Azure Pipelines
2. Also detect the test framework by looking for test configuration and dependencies:
   - `jest.config.*`, `vitest.config.*` → JavaScript test frameworks
   - `pytest.ini`, `pyproject.toml` with pytest config → pytest
   - `build.gradle*` with test config → JUnit/Gradle
   - `pom.xml` → Maven/JUnit
   - `go.mod` → Go testing
   - `Cargo.toml` → Rust
3. Call the `setup-trunk-uploads` MCP tool with the detected context.
4. Present the setup plan step by step, with copy-pasteable configuration snippets.
5. After presenting the plan, offer to apply the CI configuration changes directly.

If the repo already has Trunk uploads configured, let the user know and point them to their dashboard at https://app.trunk.io.
