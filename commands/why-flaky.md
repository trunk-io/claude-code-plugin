---
description: Explain why a test is flaky without applying changes
argument-hint: <test_name>
---

# Why Is This Test Flaky?

Use the Trunk MCP server to explain the root cause of a flaky test without making any code changes.

1. Take the test name from the arguments. If no test name is provided, ask the user which test they want to investigate.
2. Call the `fix-flaky-test` MCP tool with the test name.
3. Focus the response on **understanding**, not fixing. Explain:
   - **What the test does** — summarize the test's purpose in plain English
   - **Root cause** — why this test is non-deterministic. Categorize it (e.g., race condition, time-dependent, shared state, network dependency, test ordering, resource contention)
   - **Failure pattern** — how often it fails, whether failures cluster around certain times or conditions, any trends
   - **Blast radius** — does this flaky test block CI for other developers? How much pipeline time does it waste?
4. If relevant, point to the specific lines of code that introduce non-determinism.
5. Do NOT apply any changes. If the user wants to fix it, suggest they run `/trunk:fix-flaky <test_name>`.

Keep the explanation accessible — assume the developer may not have written this test themselves.
