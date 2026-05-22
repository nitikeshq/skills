---
name: qwegle-skills
description: Use this skill for Qwegle's spec-driven development workflow. Trigger when planning, designing, scoping, implementing, or reviewing a feature from requirements through delivery. Covers methodology, requirements using EARS, design documents, task breakdowns, AI reasoning, prompting strategy, execution, quality assurance, examples, templates, and one-agent ownership of the complete workflow.
---

# Qwegle Spec-Driven Development

This skill turns vague product intent into shipped code through one accountable agent workflow:

```
Intent -> Requirements -> Design -> Tasks -> Implementation -> QA -> PR Review -> Delivery
```

The agent owns the full process unless the user explicitly asks for only one phase. Do not split responsibility across invisible actors. Ask for input only when a decision is blocked or high-risk.

## Methodology

Spec-driven development works because it separates intent from implementation:

- **Requirements** define observable behavior and acceptance conditions.
- **Design** defines architecture, data flow, contracts, risks, and tradeoffs.
- **Tasks** define the smallest safe implementation steps.
- **Execution** implements one task at a time with verification after meaningful changes.
- **Review** checks whether the result still satisfies the spec.

Use this workflow when requirements are ambiguous, multiple files or systems are involved, architecture choices matter, user-facing behavior changes, or regressions would be expensive.

Use a lighter path for trivial edits, typo fixes, one-line config changes, or when the user explicitly asks for a direct implementation.

## Process Guide

### Phase 1: Requirements

Convert the request into testable behavior using EARS-style statements:

```
WHEN <trigger/event>
THE SYSTEM SHALL <observable behavior>

IF <condition>
THEN THE SYSTEM SHALL <observable behavior>

WHILE <state>
THE SYSTEM SHALL <ongoing behavior>
```

Capture:

- User goal and success criteria
- Functional requirements
- Non-functional requirements
- Constraints and assumptions
- Out-of-scope items
- Edge cases and failure states

Requirements are done when a reviewer could decide whether the implementation passes without reading the developer's mind.

### Phase 2: Design

Create a design that explains how the system will satisfy the requirements.

Include:

- Current-state findings from the codebase
- Proposed architecture and ownership boundaries
- Data model or API changes
- State transitions and error handling
- Security, privacy, performance, and accessibility concerns
- Testing strategy
- Migration or rollout plan when needed
- Alternatives considered and why they were rejected

Keep design proportional. Small changes need a compact design note; multi-module changes need a fuller plan.

### Phase 3: Tasks

Break design into implementation tasks that can be completed and verified independently.

Good tasks are:

- Actionable
- Ordered by dependency
- Small enough to review
- Tied to a requirement
- Paired with validation

Example:

```markdown
1. Add schema field for export status.
   - Requirement: export job status is visible to users.
   - Validation: migration applies and model tests pass.

2. Add service function for export job creation.
   - Requirement: exports are queued reliably.
   - Validation: unit test covers success and provider failure.
```

## Workflow Diagrams

### Full Flow

```
Need is unclear?
  -> clarify intent
  -> write requirements
  -> design
  -> tasks
  -> implement
  -> verify
  -> PR review
  -> deliver summary
```

### Decision Flow

```
Is this a trivial edit?
  yes -> implement directly
  no  -> write requirements

Does architecture change?
  yes -> design before code
  no  -> compact implementation plan

Does implementation affect users or shared systems?
  yes -> include tests and review checklist
  no  -> run focused validation
```

## AI Reasoning

Use explicit reasoning checkpoints:

- **Scope:** What is the smallest complete outcome?
- **Risk:** What can break across modules, users, data, or runtime?
- **Ownership:** Which layer owns each rule or operation?
- **Evidence:** What did the codebase show, and what is assumed?
- **Validation:** What proves this works?

When choices compete, evaluate:

| Factor | Prefer |
|---|---|
| Existing pattern | Match the codebase unless it is clearly harmful |
| Blast radius | Smaller, reversible changes |
| User value | Complete user-visible behavior over internal polish |
| Reliability | Explicit states, structured errors, deterministic tests |
| Maintainability | Clear boundaries over clever abstractions |

## Prompting Strategies

When collaborating with AI, prompt with:

- Goal: what outcome matters
- Context: product, stack, files, constraints
- Behavior: what should happen
- Examples: inputs, outputs, edge cases
- Validation: tests or manual checks expected
- Boundaries: what not to change

Templates:

```markdown
Build <feature> for <user/persona>.
Success means <observable outcome>.
Use existing patterns in <files/modules>.
Do not change <excluded area>.
Validate with <tests/checks>.
```

```markdown
Review this PR for requirements coverage, architecture risks, regressions, and missing tests.
Prioritize findings by severity and cite file/line references.
```

## Execution Guide

Implementation rules:

- Read the relevant code before editing.
- Keep changes scoped to the active requirement.
- Prefer existing helpers and patterns.
- Implement one task slice at a time.
- Run focused tests after meaningful changes.
- Update the task list as work completes.
- Do not hide uncertainty; record assumptions and residual risks.

Quality assurance:

- Requirements are covered by tests or explicit manual checks.
- Error paths and edge cases are exercised.
- Existing workflows still work.
- User-facing copy, accessibility, and performance are checked where relevant.
- PR description explains what changed, why, and how it was verified.

Troubleshooting:

- If requirements are unstable, return to Phase 1.
- If design feels too large, split the feature.
- If task execution stalls, identify the exact blocker and smallest next proof.
- If tests are flaky, separate product failure from environment failure.
- If AI output drifts, restate the spec and constraints before continuing.

## Resources

Standards and practices to use:

- EARS-style requirements for observable behavior.
- ADR-style tradeoff notes for important design decisions.
- PR checklists for verification and review.
- Test pyramids: unit for logic, integration for contracts, end-to-end for core workflows.

Recommended tools:

- GitHub Issues for requirements and acceptance criteria.
- GitHub Projects or similar boards for task states.
- PR templates for review consistency.
- CI checks for automated validation.
- Local scripts for repeatable setup and tests.

## Examples

### Simple Feature Spec

```markdown
Goal: Add a resend invite button.

Requirement:
WHEN an admin views a pending invitation
THE SYSTEM SHALL show a resend action.

IF the resend succeeds
THEN THE SYSTEM SHALL show a success message and update the last-sent timestamp.

IF the provider fails
THEN THE SYSTEM SHALL show a retryable error without deleting the invitation.

Design:
- Reuse existing invite mailer service.
- Keep ownership and permission checks in the action.
- Add tests for success, provider failure, and unauthorized user.

Tasks:
1. Add action endpoint.
2. Add UI button and loading state.
3. Add tests.
4. Run lint, typecheck, and focused test file.
```

### Complex System Spec

```markdown
Goal: Add asynchronous export jobs.

Requirements:
- WHEN a user requests an export, THE SYSTEM SHALL create a queued job.
- WHILE the job is running, THE SYSTEM SHALL expose status.
- IF generation fails, THEN THE SYSTEM SHALL preserve the failure reason for support.
- WHEN complete, THE SYSTEM SHALL provide a time-limited download URL.

Design:
- Add export_jobs table.
- Add service for storage provider upload.
- Add worker for generation.
- Keep user authorization in actions.
- Add status polling endpoint.

Tasks:
1. Migration and model.
2. Queue creation action.
3. Worker implementation.
4. Storage service integration.
5. Status endpoint and UI.
6. Tests and rollout checks.
```

## Templates

### Requirements Template

```markdown
# Requirements

## Goal

## Users

## Functional Requirements
- WHEN ...
  THE SYSTEM SHALL ...

## Non-Functional Requirements

## Edge Cases

## Out of Scope

## Acceptance Criteria
```

### Design Template

```markdown
# Design

## Current State

## Proposed Approach

## Architecture and Boundaries

## Data/API Changes

## Error Handling

## Security/Privacy

## Testing Strategy

## Alternatives Considered

## Rollout Plan
```

### Tasks Template

```markdown
# Tasks

- [ ] 1. <task>
  - Requirement:
  - Validation:

- [ ] 2. <task>
  - Requirement:
  - Validation:
```

## Agent Operating Contract

One agent should:

1. Understand the request.
2. Produce or infer requirements.
3. Design the solution.
4. Break the work into tasks.
5. Implement the tasks.
6. Validate with tests/checks.
7. Review the diff against the requirements.
8. Prepare or update the PR.
9. Report what changed, what passed, and what risk remains.

If the PR is open, keep it engaged: update the branch, ensure the PR description is accurate, respond to review comments, trigger configured review tools, and report when no external review tool is installed or responding.
