---
name: Plan Story
description: Turn one refined, Ready User Story into a technically actionable implementation plan after inspecting the existing system
---

# Plan Story

Generic method for planning the implementation of exactly one Ready User Story.
The plan is written for a builder who has not read the conversation.

## 1. Preconditions

Before planning, confirm:

- the Story is `ready` (value, scope, Acceptance Criteria and UX expectations are clear)
- the specification is the current revision, and record its revision or content hash
- no other Story is currently in flight (one Story at a time)

If the spec is unclear, blocking questions exist, or the Story is not ready, stop
and raise that with the human instead of planning around it.

## 2. Inspect the existing system first

Read, before writing the plan:

- product context relevant to the Story
- the Story's specification
- existing architecture and conventions
- existing code and components that the Story touches
- existing tests and test conventions
- any relevant migrations or configuration

Plan from what exists, not from what you assume exists.

## 3. Plan contents

Include only information that is useful to the builder:

- Story ID, title, spec path and specification revision or content hash
- existing-system impact
- implementation approach
- UI changes
- data / persistence changes
- security implications
- files and components likely affected
- test strategy (including which Acceptance Criteria map to which checks)
- end-to-end journey
- risks and open technical questions
- implementation sequence

## 4. Smallest complete vertical slice

Prefer the smallest complete vertical slice that satisfies the Story end to end
(UI, logic, persistence, tests) over a broad partial implementation. Avoid
speculative abstractions, unnecessary layers and infrastructure that no current
requirement justifies.

## 5. Present and wait

Present a concise plan summary to the human and wait for explicit plan approval
before any implementation begins. Planning is not a licence to build.

## 6. Approval and immutability

Write the plan as the Story's plan artefact, kept alongside its specification.

Once the human approves, record the approval in the plan's status metadata
(approver and date) and treat the plan as fixed: it is the contract that
deviations are measured against. Do not keep a second copy elsewhere, and
never overwrite another Story's plan.

Record deviations from the approved plan during implementation; meaningful
deviations must be documented in the verification evidence and the review.

## 7. Boundaries

- Do not redefine product requirements, weaken Acceptance Criteria or expand
  scope while planning. If the requirement is wrong, route it back to the
  product loop.
- Do not implement production code.
- Do not guess at missing credentials, environment or access: state the gap.
- Ask the human at most ONE question per message, and only when the answer
  materially affects the plan and cannot be determined from existing context.
