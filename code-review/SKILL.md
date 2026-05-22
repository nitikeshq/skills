---
name: greploop
description: >
  Iteratively improves a PR (GitHub), MR (GitLab), or shelved changelist (Perforce) until Greptile
  gives it a 5/5 confidence score with zero unresolved comments. Triggers Greptile review, fixes
  all actionable comments, pushes/re-shelves, re-triggers review, and repeats. Also includes
  check-pr behavior for inspecting unresolved review comments, failing status checks, and incomplete
  descriptions. Use when the user wants to fully optimize a PR/MR/CL against Greptile's code review
  standards, check a PR, address review feedback, prepare a change for submission, or run the
  Greptile review loop.
license: MIT
compatibility: Requires git and gh (GitHub CLI), glab (GitLab CLI), or p4 (Perforce CLI) installed and authenticated. greploop mode also requires Greptile installed on the repo.
metadata:
  author: greptileai
  version: "1.2"
allowed-tools: Bash(gh:*) Bash(glab:*) Bash(git:*) Bash(p4:*)
---

# Greploop

Iteratively fix a PR/MR/CL until Greptile gives a perfect review: 5/5 confidence, zero unresolved comments.

Two modes are supported:

| Mode | Invoke as | What it does |
|---|---|---|
| **greploop** | `/greploop [PR_NUMBER]` | Loop: trigger Greptile review → fix → push → repeat until 5/5 |
| **check-pr** | `/greploop check-pr [PR_NUMBER]` | Inspect PR/MR/CL for issues, optionally fix and resolve threads |

If no mode is specified, default to `greploop` when the user asks for Greptile, 5/5, benchmark, full optimization, or "until fixed." Infer `check-pr` only for inspection-only requests.

**Hard prerequisite:** greploop requires the external Greptile integration to be installed and responding on the repo. If no Greptile check, review, note, or score appears after triggering and polling, report:

```
Greploop blocked.
  Platform:      GitHub
  Iterations:    1
  Confidence:    unavailable
  Resolved:      0
  Remaining:     unknown
  Blocker:       Greptile integration did not create a check, review, comment, or score.
```

Do not invent a 5/5 score. Only report `5/5` when it is found in Greptile output.

---

## Shared: Detect Platform

Run this first in both modes.

```bash
if p4 info >/dev/null 2>&1; then
  VCS="perforce"
else
  REMOTE_URL=$(git remote get-url origin)
  if echo "$REMOTE_URL" | grep -qi "gitlab"; then
    VCS="gitlab"
  else
    VCS="github"
  fi
fi
```

For self-hosted GitLab whose hostname doesn't contain "gitlab", the user can pass `--vcs gitlab`. For Perforce, pass `--vcs perforce`.

## Shared: Identify the PR/MR/CL

If a number was provided, use it. Otherwise detect:

**GitHub:**
```bash
gh pr view --json number,headRefName -q '{number: .number, branch: .headRefName}'
```

**GitLab:**
```bash
glab mr view --output json | jq '{iid: .iid, branch: .source_branch}'
```

**Perforce:**
```bash
p4 changes -s pending -u $P4USER -c $P4CLIENT
```

Key field differences:
- GitHub: `number`, `headRefName`, `headRefOid`
- GitLab: `iid`, `source_branch`, `sha`
- Perforce: changelist number, shelved files

---

## Mode 1: check-pr

Analyze a PR/MR/CL for review comments, status checks, and description completeness, then help address issues.

### Step 1 — Fetch PR/MR/CL Details

**GitHub:**
```bash
gh pr view <PR_NUMBER> --json title,body,state,reviews,comments,headRefName,statusCheckRollup
gh api repos/{owner}/{repo}/pulls/<PR_NUMBER>/comments
```

**GitLab:**
```bash
glab mr view <MR_IID> --output json
glab api "projects/:fullpath/merge_requests/<MR_IID>/discussions"
```
Paginate discussions with `?per_page=100&page=N` if needed.

**Perforce:**
```bash
p4 describe -s <CL_NUMBER>   # description, files, status
p4 describe -S <CL_NUMBER>   # shelved files
```

### Step 2 — Wait for Pending Checks

Poll every 30 seconds until all checks reach a terminal state.

**GitHub:** poll `statusCheckRollup` from `gh pr view`. Terminal states: anything that isn't `PENDING` or `IN_PROGRESS`.

**GitLab:**
```bash
glab api "projects/:fullpath/merge_requests/<MR_IID>/pipelines"
```
Poll until no pipeline has `running` or `pending` status.

**Perforce:** No native CI checks — proceed immediately unless using an external review tool.

### Step 3 — Analyze

Evaluate these four areas:

**A. Status Checks** — Are all CI checks passing? If failing, identify which and why.

**B. PR/MR Description** — Complete? Required sections filled? Any TODOs or placeholders?

**C. Review Comments** — Inline diff comments from bots (Greptile, linters) and human reviewers.

**D. General Comments** — Discussion comments. Bot comments (deploy previews, etc.) are usually informational.

### Step 4 — Categorize Issues

| Category | Meaning |
|---|---|
| **Actionable** | Code changes, test fixes, or updates needed |
| **Informational** | FYIs or questions that don't require changes |
| **Already addressed** | Resolved by a subsequent commit |

### Step 5 — Report Findings

Present a summary table:

| Area | Issue | Status | Action Needed |
|------|-------|--------|---------------|
| Status Checks | CI build failing | Failing | Fix type error in `src/api.ts` |
| Review | "Add null check" — @reviewer | Actionable | Add guard clause |
| Description | TODO placeholder in test plan | Actionable | Fill in test plan |
| Review | "Looks good" — @teammate | Informational | None |

### Step 6 — Fix Issues (if requested)

If the user confirms:

1. Switch to the PR/MR branch (or ensure files are open in the correct CL for Perforce).
2. Make fixes.
3. Commit/push or re-shelve:

**GitHub/GitLab:**
```bash
git add <files>
git commit -m "address review feedback"
git push
```

**Perforce:**
```bash
p4 edit <file>
# make changes
p4 shelve -f -c <CL_NUMBER>
```

### Step 7 — Resolve Review Threads

**GitHub** — fetch unresolved thread IDs (see [GraphQL reference](references/graphql-queries.md)), then batch-resolve:
```bash
gh api graphql -f query='
mutation {
  t1: resolveReviewThread(input: {threadId: "ID1"}) { thread { isResolved } }
  t2: resolveReviewThread(input: {threadId: "ID2"}) { thread { isResolved } }
}'
```

**GitLab** — fetch unresolved discussions (see [GitLab API reference](references/gitlab-api.md)), resolve each individually:
```bash
glab api --method PUT \
  "projects/:fullpath/merge_requests/<MR_IID>/discussions/<DISCUSSION_ID>" \
  --field resolved=true
```

**Perforce** — no native thread resolution. Update the CL description or respond in your review tool (Swarm, etc.).

### check-pr Output Format

```
PR #123 — "Add null safety to auth module"
Platform:        GitHub
Status checks:   2 passing, 1 failing (lint)
Review comments: 3 actionable, 1 informational
Description:     Incomplete (missing test plan)

Actionable items:
  1. Fix lint error in src/auth.ts:45
  2. Add null check per @reviewer comment
  3. Fill in test plan section

Informational:
  - "Looks good overall" — @teammate (no action needed)
```

---

## Mode 2: greploop

Iteratively improve a PR/MR/CL until Greptile gives 5/5 confidence with zero unresolved comments. **Max 5 iterations.**

### The Loop

Repeat until exit condition met:

#### A. Push / Re-shelve

**GitHub/GitLab:**
```bash
git push
sleep 5
```

**Perforce:**
```bash
p4 shelve -f -c <CL_NUMBER>
sleep 5
```

#### B. Trigger Greptile Review

**GitHub** — only trigger if not already running:
```bash
GREPTILE_STATE=$(gh pr checks <PR_NUMBER> --json name,state | jq -r '.[] | select(.name | test("greptile"; "i")) | .state')
if [ "$GREPTILE_STATE" != "PENDING" ] && [ "$GREPTILE_STATE" != "IN_PROGRESS" ]; then
  gh pr comment <PR_NUMBER> --body "@greptile review"
fi
```

Poll until the Greptile check run completes:
```bash
HEAD_SHA=$(gh pr view <PR_NUMBER> --json headRefOid -q .headRefOid)
while true; do
  STATUS=$(gh api "repos/{owner}/{repo}/commits/$HEAD_SHA/check-runs" \
    --jq '.check_runs[] | select(.name | test("greptile"; "i")) | .status' 2>/dev/null)
  [ "$STATUS" = "completed" ] && break
  echo "Waiting for Greptile... ($STATUS)"; sleep 10
done
```

**GitLab** — only trigger if no pipeline running:
```bash
RUNNING=$(glab api "projects/:fullpath/merge_requests/<MR_IID>/pipelines" | \
  jq '[.[] | select(.status == "running" or .status == "pending")] | length')
[ "$RUNNING" = "0" ] && glab mr note <MR_IID> --message "@greptile review"
```

Poll until the Greptile job completes (see [GitLab API reference](references/gitlab-api.md)).

**Perforce** — if Greptile is webhook-triggered on `p4 shelve`, poll your Greptile dashboard or Swarm for the review result.

#### C. Fetch Greptile Score and Comments

Check both the PR body and reviews/notes for the most recent score.

**GitHub:**
```bash
gh pr view <PR_NUMBER> --json body -q '.body'
gh api repos/{owner}/{repo}/pulls/<PR_NUMBER>/reviews  # look for greptile-apps[bot]
gh api repos/{owner}/{repo}/pulls/<PR_NUMBER>/comments # unresolved inline comments
```

**GitLab:**
```bash
glab mr view <MR_IID> --output json | jq -r '.description'
glab api "projects/:fullpath/merge_requests/<MR_IID>/notes"           # look for Greptile bot user
glab api "projects/:fullpath/merge_requests/<MR_IID>/discussions"     # unresolved DiffNote threads
```

**Perforce:**
```bash
p4 describe -s <CL_NUMBER>   # check description for appended Greptile score block
# If using Swarm: GET /api/v11/comments?topic=reviews/<REVIEW_ID>
```

Parse the text for:
- **Score**: pattern like `3/5` or `Confidence: 5/5`
- **Unresolved comment count**

#### D. Check Exit Conditions

Stop if **any** of these:
- Score is **5/5** AND zero unresolved comments → success
- Max 5 iterations reached → report current state

#### E. Fix Actionable Comments

For each unresolved Greptile comment:
1. Read the file in context.
2. Classify as actionable (fix needed) or informational (note and resolve anyway).
3. Make the fix if actionable.

#### F. Resolve Threads

**GitHub** — batch-resolve via GraphQL (see [GraphQL reference](references/graphql-queries.md)):
```bash
gh api graphql -f query='
mutation {
  t1: resolveReviewThread(input: {threadId: "ID1"}) { thread { isResolved } }
  t2: resolveReviewThread(input: {threadId: "ID2"}) { thread { isResolved } }
}'
```

**GitLab** — resolve each discussion individually (see [GitLab API reference](references/gitlab-api.md)):
```bash
glab api --method PUT \
  "projects/:fullpath/merge_requests/<MR_IID>/discussions/<DISCUSSION_ID>" \
  --field resolved=true
```

#### G. Commit and Go Back to A

**GitHub/GitLab:**
```bash
git add -A
git commit -m "address greptile review feedback (greploop iteration N)"
git push
```

**Perforce:**
```bash
p4 shelve -f -c <CL_NUMBER>
```

### greploop Output Format

On success:
```
Greploop complete.
  Platform:      GitHub
  Iterations:    2
  Confidence:    5/5
  Resolved:      7 comments
  Remaining:     0
```

If max iterations hit:
```
Greploop stopped after 5 iterations.
  Platform:      GitLab
  Confidence:    4/5
  Resolved:      12 comments
  Remaining:     2

Remaining issues:
  - src/auth.ts:45 — "Consider rate limiting this endpoint"
  - src/db.ts:112 — "Missing index on user_id column"
```

---

## Reference Files

- [`references/gitlab-api.md`](references/gitlab-api.md) — GitLab REST API calls for discussions, pipelines, and resolution
- [`references/graphql-queries.md`](references/graphql-queries.md) — GitHub GraphQL queries for fetching and resolving review threads
