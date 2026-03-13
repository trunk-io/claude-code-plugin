---
description: Get root cause analysis and apply a fix for a flaky test
argument-hint: <test_name>
---

# Fix Flaky Test

Use the Trunk MCP server to retrieve root cause analysis and fix a flaky test.

1. Take the test name from the arguments. If no test name is provided, ask the user which test they want to fix.
2. Call the `fix-flaky-test` MCP tool with the test name.
3. Present the root cause analysis clearly:
   - What the test does
   - Why it's flaky (the root cause)
   - How often it fails and any historical patterns
4. Show the suggested fix with a clear explanation of what changes and why.
5. Ask the user if they'd like to apply the fix. If yes, make the code changes directly.
6. After applying, suggest running the test to verify the fix works.

If the MCP server returns no results, suggest that the user:
- Check the test name spelling
- Verify their repo is connected to Trunk (run `/trunk:setup-uploads` if not)
- Check https://app.trunk.io to see if the test appears in their dashboard
