---
name: code-structure
description: Use this skill whenever you're designing or refactoring backend code architecture — especially when multiple workflows duplicate operational logic, when deciding what belongs in actions vs shared services, when extracting repeated blocks across domain flows, or when adding features that share mechanics with existing ones. Trigger this skill for questions like "where should this logic live?", "how do I avoid copy-pasting this?", "should this be a service?", "how do I structure this new feature?", or any time someone is untangling messy action/handler files. Also use when reviewing PRs for architectural smells like god services, leaky services, or over-abstraction.
---

# Service Layer Architecture

## Overview

**Two-layer separation:** Actions orchestrate domain rules (the "why/when"), while a service layer centralizes reusable operational mechanics (the "how").

This prevents duplicated code, inconsistent behavior, and bugs fixed in one path but not others.

## When to Use This Pattern

**Use it when:**
- Multiple callers need the same low-level operation (sandbox creation, email sending, payment processing)
- You're copy-pasting operational logic between action files
- A bug fix in one workflow doesn't propagate to others doing the same thing
- Adding a new feature that shares mechanics with existing flows

**Don't use it when:**
- Logic is truly domain-specific and used by only one caller (over-abstraction is a real cost)
- The "shared" operation has subtle differences per caller — force-sharing leads to flag-riddled service functions

## Core Separation

```
Orchestration Layer (Actions)          Service Layer (Shared Mechanics)
├── owns business rules                ├── owns reusable operations
├── owns state transitions             ├── owns provider/SDK interactions
├── owns auth/ownership checks         ├── owns command execution details
├── owns failure classification        ├── owns health checks / readiness
├── owns retries / user-facing errors  └── returns structured results
└── calls service functions
```

**Rule of thumb:**
- "What this product flow means" → keep in actions
- "How to do this operation reliably" → move to service layer

## Decision Checklist

Before extracting to a service, answer these:

| Question | Extract if... | Keep in action if... |
|---|---|---|
| How many callers? | 2 or more now or soon | Only 1 caller today |
| Does behavior differ per caller? | Caller configures via params | Logic branches per caller internally |
| Does it touch DB/state? | Reads only, or via passed-in repo | Needs to mutate domain records |
| Is it a provider interaction? | Yes (email, S3, Stripe, etc.) | No, pure domain logic |
| Would a bug here affect multiple flows? | Yes | No |

## Designing Service Functions

Design as **capability blocks**, not monoliths:

```ts
// Good: composable — each caller chooses what to invoke
createManagedSandbox(...)
prepareRepo(...)
detectPackageManager(...)
installDependencies(...)
runBuildCommand(...)
startSandboxRuntime(...)

// Bad: one function that does all of the above (caller can't opt out of any step)
bootstrapEntireSandboxEnvironment(...)
```

Each service function should:
- Accept all required data as **explicit parameters** (no hidden global state)
- Return **structured outputs** — e.g., `{ ready: boolean, previewUrl: string, proxyPort: number }`
- Never reach into the database or domain state directly
- Make failure explicit — structured results or thrown errors, not swallowed silently

## Structured Return Pattern

Prefer structured results over boolean success/throw-only patterns, especially for operations that can partially succeed:

```ts
// emailService.ts
type SendResult =
  | { ok: true; messageId: string }
  | { ok: false; reason: "invalid_address" | "provider_error"; detail: string };

export async function sendWelcomeEmail(params: {
  to: string;
  name: string;
}): Promise<SendResult> {
  try {
    const result = await emailProvider.send(params.to, "Welcome", buildHtml(params.name));
    return { ok: true, messageId: result.id };
  } catch (err) {
    return { ok: false, reason: "provider_error", detail: String(err) };
  }
}
```

This lets **actions** own the failure classification and user-facing error messaging:

```ts
// userSignup.ts
if (user.marketingOptIn) {
  const result = await sendWelcomeEmail({ to: user.email, name: user.name });
  if (!result.ok) {
    logger.warn("Welcome email failed", result); // action decides: log and continue
  }
}

// adminInvite.ts
const result = await sendWelcomeEmail({ to: invitee.email, name: invitee.name });
if (!result.ok) {
  throw new InviteDeliveryError(result.detail); // action decides: hard fail
}
```

Same service function — different failure handling per caller.

## Service Layer Boundaries

**Services should NOT:**
- Mutate domain records (no `db.user.update(...)` inside a service)
- Know about HTTP request/response objects
- Throw user-facing error messages (those belong in the action)
- Have conditional logic that encodes business policy ("if user is premium, do X")

**Services CAN:**
- Call external providers (Stripe, S3, SendGrid, Twilio)
- Run shell commands, manage child processes
- Perform read queries if they need data to do their job (pass in the repo or data directly)
- Have internal retry logic for transient provider failures

## Migration Checklist

When extracting shared logic from existing action code:

1. Write the flow in action code first — understand behavior before abstracting
2. Mark repeated operational chunks across 2+ callers
3. Extract **only** the repeated, non-domain chunks to the service
4. Replace **one caller** → verify all tests pass → replace remaining callers
5. Keep domain policy in actions: auth, status transitions, error classification
6. Run: typecheck, lint, integration tests across all affected flows

**Don't refactor everything at once.** One caller migrated and verified beats a broken multi-file diff.

## Quick Reference

| Design Principle | Do | Don't |
|---|---|---|
| API shape | Composable capability blocks | One giant "do everything" method |
| Inputs | Explicit params | Hidden global state or DB access |
| Outputs | Structured typed returns | Booleans / swallowed errors |
| Domain logic | Keep auth, policy, classification in actions | Let service encode business rules |
| Failure handling | Action classifies; service surfaces | Service throws user-facing strings |
| Extraction trigger | Logic repeated across 2+ callers | Logic used by only 1 caller |
| Migration | One caller at a time | Rewrite everything in one PR |

## Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|---|---|---|
| **God service** | One function with 10+ steps and branching per caller | Split into composable capability blocks |
| **Leaky service** | Service calls `db.orders.update(...)` directly | Pass in a repo/handler, or lift DB write to action |
| **Flag soup** | `sendEmail({ ..., skipForAdmins: true, legacyFormat: false })` | Two separate service functions, or caller builds the payload |
| **Over-abstraction** | Extracted logic used by exactly one caller | Delete the service fn; inline it back |
| **Policy creep** | Service checks `if (user.role === 'admin')` | Move that check to the action; pass only what's needed |

## Full Example: Sandbox Provisioning

Two workflows — `createProject` and `forkTemplate` — both need to spin up a sandbox. Without a service layer, each duplicates the provisioning logic. With one:

```ts
// sandboxService.ts — shared mechanics
export async function createManagedSandbox(params: {
  repoUrl: string;
  branch: string;
  env: Record<string, string>;
}): Promise<{ sandboxId: string; previewUrl: string; ready: boolean }> {
  const sandbox = await sandboxProvider.create({ repo: params.repoUrl, branch: params.branch });
  await sandboxProvider.injectEnv(sandbox.id, params.env);
  const ready = await sandboxProvider.waitForReady(sandbox.id, { timeoutMs: 30_000 });
  return { sandboxId: sandbox.id, previewUrl: sandbox.url, ready };
}

// createProject.ts — action owns the business rules
export async function createProject(input: CreateProjectInput, ctx: Context) {
  await assertOwnership(ctx.user, input.orgId);          // auth: action's job
  const project = await db.projects.create({ ...input }); // state: action's job

  const result = await createManagedSandbox({            // mechanic: service's job
    repoUrl: project.repoUrl,
    branch: "main",
    env: buildEnvVars(project),
  });

  if (!result.ready) {
    await db.projects.update(project.id, { status: "provisioning_failed" }); // action classifies failure
    throw new ProjectProvisioningError(project.id);
  }

  await db.projects.update(project.id, { sandboxId: result.sandboxId, status: "active" });
  return project;
}

// forkTemplate.ts — different business rules, same mechanic
export async function forkTemplate(input: ForkInput, ctx: Context) {
  await assertPlanLimit(ctx.user, "sandbox_count");      // different policy

  const fork = await db.projects.fork(input.templateId, ctx.user.id);
  const result = await createManagedSandbox({            // same service function
    repoUrl: fork.repoUrl,
    branch: input.targetBranch,
    env: buildEnvVars(fork),
  });

  // forkTemplate tolerates not-ready sandboxes; createProject does not — action's call
  await db.projects.update(fork.id, {
    sandboxId: result.sandboxId,
    status: result.ready ? "active" : "warming_up",
  });
  return fork;
}
```

## Mental Model

```
New feature?
  └─ Write in action first
       ├─ See the same operational block in another action? → Extract to service
       └─ Used only here?                                  → Keep in action

Service function getting large?
  └─ Does it branch on caller identity or business policy?
       ├─ Yes → split into smaller capability blocks; move policy to actions
       └─ No  → it's fine; complexity comes from the problem, not the design
```

**One sentence:** Actions orchestrate domain rules; the service layer centralizes reusable operational mechanics with a composable, explicit-input, structured-output API.
