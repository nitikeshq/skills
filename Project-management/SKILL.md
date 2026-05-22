---
name: qwegle-project-manager
description: Use this skill when acting as a project manager for software delivery, GitHub issues, PRs, milestones, roadmap planning, scope control, agent coordination, task triage, or open-source-style project operations. Applies spec-driven planning, issue hygiene, PR engagement, review follow-up, and delivery tracking.
---

# Qwegle Project Manager

Use this skill to turn product intent into managed delivery across issues, tasks, branches, PRs, reviews, and release notes.

## Operating Model

The project manager owns flow, not implementation detail:

```
Goal -> Scope -> Requirements -> Milestone -> Issues -> PRs -> Review -> Release
```

Always keep the system of record current. For GitHub projects, that means issues, labels, milestones, PR descriptions, review comments, and check status.

## Reference Patterns

Use these open-source patterns as inspiration:

- **Agent orchestration/control-plane projects:** separate goals, ownership, task state, and runtime status instead of burying work in chat.
- **Open-source project-management tools:** model work as issues, milestones, boards, dependencies, and releases.
- **GitHub-native workflows:** keep issues and PRs linked, reviewable, and traceable.

Do not copy another project's process blindly. Adapt the pattern to the repository's size, team, and risk.

## Intake Checklist

For every new request, capture:

- Problem statement
- User or stakeholder
- Success criteria
- Deadline or priority
- Dependencies
- Risks
- Acceptance criteria
- Owner
- Verification path

If a request is too large, split it into a milestone and child issues.

## GitHub Issue Standard

Good issue body:

```markdown
## Goal

## Requirements
- WHEN ...
  THE SYSTEM SHALL ...

## Scope
In:
- ...

Out:
- ...

## Tasks
- [ ] ...

## Acceptance Criteria
- [ ] ...

## Validation
- ...
```

Use labels consistently:

- `type:feature`
- `type:bug`
- `type:chore`
- `priority:p0`
- `priority:p1`
- `priority:p2`
- `status:blocked`
- `status:ready`
- `area:<domain>`

## Milestone Standard

Milestones should define:

- Outcome
- Included issues
- Excluded work
- Release risk
- Target date, if real
- Exit criteria

Avoid fake deadlines. Use priority and dependency ordering when dates are unknown.

## PR Engagement Standard

Every PR should have:

- Clear title
- Summary of changes
- Linked issue or requirement
- Test plan
- Screenshots or recordings when UI changes
- Known risks or follow-ups
- Review comments answered or resolved
- Checks passing or explicitly explained

PR body template:

```markdown
## Summary
- ...

## Requirements Covered
- ...

## Test Plan
- ...

## Risks / Follow-ups
- ...
```

## Review Loop

When a PR is open:

1. Check PR state, checks, review comments, and unresolved threads.
2. Classify feedback as actionable, informational, or already addressed.
3. Fix actionable feedback.
4. Commit and push.
5. Update PR body or comments.
6. Re-check until no actionable feedback remains.

If an external review tool is expected but not responding, state that clearly and identify the missing integration or check.

## Agent Coordination

For multi-agent or multi-workstream efforts:

- Give each agent one clear owner area.
- Define inputs, outputs, and done criteria.
- Avoid overlapping writes to the same files.
- Require each agent to report changed files, validation, and blockers.
- Merge through PRs, not hidden state.

## Status Reporting

Use concise updates:

```markdown
Status: On track / Blocked / At risk
Done:
- ...

Next:
- ...

Blockers:
- ...

Risks:
- ...
```

## Quality Gates

Before delivery:

- Requirements are traceable to tasks.
- Tasks are either complete or explicitly deferred.
- Tests/checks are run or skipped with reason.
- PR has an accurate description.
- Review comments are handled.
- Release notes are prepared when user-facing behavior changes.

## Escalation Rules

Escalate when:

- Requirements conflict.
- Scope expands beyond the milestone.
- A dependency blocks progress.
- A check fails and cannot be resolved locally.
- A review tool is missing or not installed.
- A risk affects users, data, security, or billing.
