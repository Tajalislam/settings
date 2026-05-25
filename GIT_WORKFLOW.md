# Git Workflow Cheat Sheet

Our team's branching strategy, naming rules, and daily workflow. **Read this before pushing.**
<img width="1440" height="1360" alt="Image" src="https://github.com/user-attachments/assets/df3091b6-df46-4eb1-aea3-fbe50560e914" />

---

## 🌳 Branch Structure

We have **3 protected branches** (no direct pushes, PR-only) and **4 types of feature branches**.

```
prod              ← Production · locked · requires 2 approvals
  ↑
stg               ← Staging / QA · requires 1 approval
  ↑
dev               ← Integration branch · requires 1 approval
  ↑
feature branches  ← Your work lives here
```

### Protected branches

| Branch | Purpose | Merges from | Approvals |
|--------|---------|-------------|-----------|
| `prod` | Live production | `stg` only | 2 |
| `stg`  | Staging / QA | `dev` only | 1 |
| `dev`  | Active integration | feature branches | 1 |

### Feature branches

| Prefix | Purpose | Branch off | Merge into |
|--------|---------|------------|------------|
| `feat/` | New feature | `dev` | `dev` |
| `fix/` | Bug fix | `dev` | `dev` |
| `hot_fix/` | Urgent production fix | **`prod`** | `prod` → `stg` → `dev` |
| `breaking_changes/` | Breaking change | `dev` | `dev` (needs 2 approvals) |

---

## 📝 Branch Naming Convention

Format: `prefix/short_work_title`

### Rules

- ✅ Lowercase only
- ✅ Use `_` (underscore) between words
- ✅ Keep title short (3–5 words)
- ✅ Be descriptive, not cryptic
- ❌ No spaces
- ❌ No special characters
- ❌ No CamelCase or kebab-case

### Examples

```bash
feat/user_profile_page
feat/checkout_flow
feat/dark_mode_toggle

fix/cart_total_calculation
fix/login_redirect_bug
fix/mobile_navbar_overflow

hot_fix/payment_crash
hot_fix/auth_token_expiry

breaking_changes/api_v2_migration
breaking_changes/auth_rewrite
```

---

## 🚀 Daily Workflow

### Morning: sync before starting work

```bash
# Always start by syncing your local branches with the remote
git checkout dev
git pull origin dev

# Create your feature branch and start working
git checkout -b feat/user_profile_page

# After completing your work, push to remote and open a PR to dev
git add .
git commit -m "feat(profile): add avatar upload component" # use conventional commit format link
git push -u origin feat/user_profile_page
# Open PR on GitHub → dev
# If you see any conflicts, resolve it your branch and push again before requesting review
# If you are confident that your code is okey instead of dev branch's code, this time use 
apply current changes. # This will override dev branch's code with your code. else use
apply incoming changes. # This will override your code with dev branch's code.
apply both changes. # This will keep both codes and you can edit the code to make it work.
# After complete the PR, Your job is done. Forwor to next development task. Don't forget to pull latest dev branch before start next task.
```

---

## 💬 Commit Message Convention

Format: `type(scope): short description`

| Type | When to use |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `style` | CSS / visual only (no logic change) |
| `refactor` | Code change, no behavior change |
| `chore` | Tooling, deps, config |
| `docs` | Documentation only |
| `test` | Tests only |

### Examples

```
feat(login): add OAuth google button
fix(checkout): prevent double-submit on slow network
style(dashboard): align card spacing on mobile
refactor(api): extract user service from controller
chore(deps): bump next.js to 14.2
```

## 🔀 Pull Request Rules

### Title format

```
[PROJ-123] feat(scope): short description
```

### Size cap

- **Target under 400 lines of diff**
- Split anything larger into multiple PRs

### Required before merge

- ✅ 1 approval (not the PR author)
- ✅ CI passing (lint + tests + build)
- ✅ Branch synced with latest `dev`
- ✅ No console errors / warnings
- ✅ Tested on mobile + desktop

### Merge strategy

**Always use "Squash and merge"** into `dev`. This collapses your WIP commits into one clean commit.

### After merge

- Delete the branch (GitHub can auto-delete)
- Pull latest `dev` immediately

---

## 👥 Role Responsibilities

### 🎨 Designer
- Branches `design/...` or `feat/...` off latest `dev`
- Builds UI with **mock data only** (hardcoded JSON, no real API calls)
- **One PR per page or component** — small and frequent
- PR goes directly to `dev`, NOT to another developer's branch

### 🔧 Full Stack
- Branches `feat/...` off latest `dev` (which already has the designer's merged UI)
- Replaces mocks with real API calls
- Adds backend logic and database changes
- PR goes to `dev`

### ⚙️ DevOps / Backend / Frontend
- Branches `feat/...` or `fix/...` off `dev`
- Handles schema, infra, CI/CD, deployment scripts
- PR goes to `dev`

---

## 🔥 Hotfix Special Case

`hot_fix/` is the **only branch type that branches off `prod`** instead of `dev`. Use it when production is on fire.
- After merging a hotfix to `prod`, immediately merge that hotfix branch into `stg` and `dev` to keep them in sync.
---

## 🛡️ Branch Protection Setup

**GitHub → Settings → Branches**, add a rule for each of `prod`, `stg`, `dev`:

- ✅ Require a pull request before merging
- ✅ Require status checks to pass (CI)
- ✅ Require branches to be up to date before merging
- ✅ Require linear history
- ✅ Block force pushes
- ✅ Block deletions

**Additional for `prod`:**
- ✅ Require 2 approvals
- ✅ Require code owner review

---

## 🔧 Recommended Tooling

Set these up once, save yourself hundreds of conflicts:

| Tool | Purpose |
|------|---------|
| **Prettier** | Auto-format code on save |
| **ESLint** | Catch errors before commit |
| **Husky** | Pre-commit hooks |
| **lint-staged** | Run linters only on staged files |
| **commitlint** | Enforce commit message convention |

## 🚫 Common Mistakes to Avoid

1. **Branching off your teammate's branch** — always branch off `dev` (or `prod` for hotfix)
2. **Letting your branch drift for days** — pull `dev` into your branch daily
3. **One giant PR with 20 files changed** — split into multiple small PRs
4. **Pushing directly to `dev`, `stg`, or `prod`** — always go through a PR
5. **Designer giving full stack a "pull from my branch" task** — designer's branch should be merged into `dev` first, then full stack branches off `dev`
6. **Forgetting to propagate hotfixes** — after merging to `prod`, merge into `stg` and `dev` too
7. **Vague commit messages** like `"update"` or `"fix stuff"` — use conventional commits
8. **Not testing on mobile** — always check your UI on both desktop and mobile before requesting review
9. **Ignoring merge conflicts** — resolve them immediately, don't let them pile up
10. **Not deleting branches after merge** — clean up to keep repo tidy
