# NN — <Verb phrase> — tester test cases

<!-- Rename this file to NN-<use-case-slug>.tester.md to match the use-case file. Rules: ../../../conventions.md, "Test cases". -->

- Use case: [`../NN-<use-case>.md`](../NN-<use-case>.md)
- Screen design: [`../ui-ux.md`](../ui-ux.md)
- Fixtures: [`README.md`](./README.md#test-data)

<!-- These cases are for testers working through the admin dashboard, manually or with UI automation.
- No endpoints, request bodies, HTTP codes or SQL.
- Give the exact text the user sees, plus the visible outcome.
- Cover the main flow, every acceptance criterion a user can reach, permissions (log in as a limited user), the feedback states and the screen sizes from ui-ux.md.
- Never invent behaviour. If the spec doesn't define an expected result, raise it as an open question in the use case first. -->

## Test cases

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `{{FEATURE_CODE}}-NN-T01` | AC 1 | <state, using the named fixtures> | <plain actions, e.g. "Log in as A-ADMIN, open Setting → Location, click Add Location…"> | <exact message, e.g. **Location created**, and what changes on screen> |

## End-to-end run (optional)

<!-- A short sequence that reuses a few fixtures to run several cases in one sitting. -->

## Reporting a failure

A difference between the actual and the expected result is a bug. Record in `RMS_API/specs/<feature>/test-results.md` and the bug tracker:
- the test ID;
- the fixture names and the actual names or values used;
- the browser and screen width;
- what the screen showed;
- a screenshot.
