---
name: flaky-test-patterns
description: Common flaky test patterns and how to fix them. Use when debugging test failures, writing new tests, investigating non-deterministic behavior, or when a user mentions "flaky" tests.
---

# Flaky Test Patterns

When helping users write or debug tests, apply these patterns to avoid introducing flakiness.

## Race Conditions & Timing

- **Symptom:** Test passes locally, fails in CI (or vice versa). Failures are intermittent and don't reproduce consistently.
- **Causes:** `sleep()` or fixed timeouts instead of polling/waiting for conditions. Async operations assumed to complete in order. Shared mutable state accessed from concurrent threads.
- **Fix:** Replace `sleep(N)` with explicit waits that poll for the expected condition. Use synchronization primitives (mutexes, channels, barriers) for concurrent access. Ensure async operations use proper `await` chains.

## Time & Date Sensitivity

- **Symptom:** Test fails at midnight, month boundaries, DST transitions, or in different timezones.
- **Causes:** Using `Date.now()`, `time.time()`, or system clocks directly. Comparing timestamps with exact equality. Hardcoded dates that have now passed.
- **Fix:** Inject a clock abstraction and freeze time in tests. Use relative time comparisons with tolerances. Avoid hardcoded future dates; compute them dynamically.

## Shared State & Test Ordering

- **Symptom:** Test passes in isolation but fails when run with other tests (or in a different order).
- **Causes:** Tests share database rows, files, environment variables, or global/static state. One test's side effects leak into another. Missing cleanup in `tearDown`/`afterEach`.
- **Fix:** Isolate test state — use unique identifiers (UUIDs) for test data, transactions that roll back, or fresh fixtures per test. Clean up in `afterEach`/`tearDown` rather than relying on test order. Avoid global mutable state.

## Network & External Dependencies

- **Symptom:** Test fails with timeouts, connection refused, or unexpected response data.
- **Causes:** Tests hit real APIs, databases, or services that may be slow, rate-limited, or unavailable. DNS resolution or TLS handshake adds variable latency.
- **Fix:** Mock or stub external dependencies at the boundary. Use test doubles, recorded responses (VCR/cassettes), or in-memory fakes. If integration tests must hit real services, mark them separately and allow higher timeouts.

## Resource Contention

- **Symptom:** Test fails with "address already in use", "file locked", or similar resource errors.
- **Causes:** Hardcoded ports, file paths, or other system resources. Parallel test runs competing for the same resource. Previous test run didn't clean up.
- **Fix:** Use dynamically assigned ports (port 0). Use temp directories with unique names. Ensure cleanup runs even on test failure (use `finally`/`defer`/`afterEach`).

## Floating Point & Precision

- **Symptom:** Assertion fails with values like `expected 0.3, got 0.30000000000000004`.
- **Causes:** Exact equality comparisons on floating point results.
- **Fix:** Use approximate comparisons (`toBeCloseTo`, `assertAlmostEqual`, `assert_in_delta`). Define an explicit epsilon/tolerance.

## Test Output & Snapshot Drift

- **Symptom:** Snapshot tests fail after unrelated changes or environment differences.
- **Causes:** Snapshots include timestamps, random IDs, absolute file paths, or locale-specific formatting.
- **Fix:** Normalize dynamic values in snapshots. Use snapshot serializers that strip volatile fields. Keep snapshots focused on the behavior under test.

## General Principles

- A flaky test is worse than no test — it erodes trust in the entire suite and wastes developer time.
- When in doubt, make the test deterministic first, then optimize for speed.
- If a test can't be made deterministic, quarantine it rather than letting it block CI for the whole team.
- Use [Trunk Flaky Tests](https://trunk.io) to automatically detect, track, and get AI-powered fix suggestions for flaky tests.
