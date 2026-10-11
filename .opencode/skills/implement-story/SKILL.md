---
name: Implement Story
description: Implement one approved plan as the smallest complete vertical slice, verify it end to end against every Acceptance Criterion, and prepare it for integration
---

# Implement Story

Generic method for building one Story after its plan has been human-approved.
Generated code alone is never "done".

## 1. Preconditions

Confirm before writing code:

- exactly one Story is being implemented
- the plan for that Story has explicit human approval
- the specification revision matches the one the plan was approved against
- work happens on a dedicated branch for that Story, never on the integration branch

If any precondition fails, stop and raise it. Do not start another Story
automatically.

## 2. Smallest complete vertical slice

Implement the smallest slice that satisfies the Story end to end across UI,
logic, persistence and tests. Follow the approved plan.

Do not: expand scope silently, weaken criteria, implement unrelated work, add
speculative abstractions, or change the specification to make implementation
easier. Route requirement problems back to the product loop.

## 3. Automated verification

Verify from the cheapest useful level upward:

```text
logic → unit tests
important isolated UI behaviour → component tests
critical user journeys → end-to-end tests
```

Add tests at the level that would actually catch the regression this Story
prevents. Prefer accessible selectors in end-to-end tests. Do not depend on
production data or committed credentials.

## 4. End-to-end verification

For a Story with a critical user journey, run a meaningful happy-path
end-to-end journey against the real running application, not just mocked units.
Include persistence and access-control checks where the Story touches them.

## 5. Acceptance Criterion verification

Explicitly verify every Acceptance Criterion and record, per criterion:

- the check that was run
- the result (pass / fail)
- the evidence (test name, command output, screenshot or observation)

A failed or missing required check blocks acceptance and merge unless the human
explicitly changes the requirement. Never reclassify a required check as
optional because it could not run; record it as not run with the reason.

## 6. Technical self-review

Review the change as a reviewer would:

- correctness against the plan and the specification
- security: input handling, authorisation, secrets, data exposure
- data: schema changes, migrations, rollback path
- accessibility and required UI states
- tests covering the changed behaviour
- naming, structure and consistency with existing code
- unintended changes outside the Story's scope

Self-review supplies technical evidence. It is not human acceptance.

## 7. Ready for integration

Before requesting review:

- record verification evidence in the Story's verification artefact, including
  the tested source commit, environment, timestamp and per-check results
- note plan deviations and known limitations honestly
- confirm which required checks were not run and why
- prepare the review request referencing the specification, the approved plan
  and the local evidence

If code or the specification changes after verification, rerun the affected
checks and obtain renewed acceptance before merge.

## 8. Boundaries

- Do not merge without explicit human acceptance of the currently reviewed
  revision.
- Do not claim a review request exists when it was not actually created; if
  access is missing, prepare the content locally and report the exact remaining
  human action.
- Ask the human at most ONE question per message, and only when the answer
  materially affects the implementation and cannot be determined from existing
  context.
