# NN — <Verb phrase> — developer test cases

<!-- Rename this file to NN-<use-case-slug>.developer.md to match the use-case file. Rules: ../../../conventions.md, "Test cases". -->

- Use case: [`../NN-<use-case>.md`](../NN-<use-case>.md)
- API: [`../api-contract.md`](../api-contract.md) · response shapes: [`project-overview.md` → API conventions](../../../project-overview.md#api-conventions)
- Data model: [`../data-model.md`](../data-model.md)
- Fixtures and tokens: [`README.md`](./README.md#test-data)

<!-- These cases are for developers calling the API directly (Postman / Postman CLI), including input the UI can't produce.
- Each case states the caller (token + Origin), the request and what is manipulated, and the expected result.
- The expected result is the HTTP status, `status` / exact `message`, and the database state after the call.
- A rejected request leaves no trace: nothing is written, and nothing changes.
- Cover every category for every endpoint this use case touches; mark a category that doesn't apply as n/a, with a reason, in the README endpoint checklist.
- Never invent behaviour. If the spec doesn't define an expected result, raise an open question in the use case and mark the case `Blocked: <question>`. -->

## Test cases

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `{{FEATURE_CODE}}-NN-D01` | Authentication | no token · HOST-A | `POST /<Controller>/<Method>` with a valid body | 401 `{ "message": "Unauthorized" }`; nothing written |
| `{{FEATURE_CODE}}-NN-D02` | Company isolation | `TOKEN_A_ADMIN` · HOST-A | body uses an ID belonging to CO-B | <status, exact message; CO-B row unchanged; no CO-B data in response> |

## Categories

Defined in [`../../../conventions.md`](../../../conventions.md) under "Test cases": Authentication · Company isolation · SuperAdmin boundary · Permission · Input validation · Business-rule bypass · Over-posting · Concurrency / idempotency · Injection / encoding · Error hygiene.
