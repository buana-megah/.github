# Contributing Guidelines

Welcome to the project! To keep our codebase stable and Git history clean, all contributors must follow the branching model, commit conventions, and Pull Request (PR) workflow outlined below.

---

## 1. Branching Strategy

We follow an **Environment-Backed GitFlow** model. Direct pushes to long-lived branches (`main`, `staging`, `develop`) are strictly forbidden. All code enters through short-lived branches and Pull Requests.

```text
[main / production] <─── (Release PR + SemVer Tag v1.x.x) ─── [staging] <─── (Promotion PR) ─── [develop]
                                                                                                  ▲
                                                                                                  │ (Squash & Merge PR)
                                                                                                  │
                                                                           [feat/* | fix/* | refactor/* | chore/*]
```

### Long-Lived Branches (Protected)
- **`main` (Production):**
  * Contains code currently running in production.
  * Never branch directly from `main` (except for emergency `hotfix/*` branches).
  * Deployments to production are triggered exclusively by pushing an annotated Semantic Version tag (e.g., `v1.2.0`).
- **`staging` (Staging / Pre-Production):**
  * Mirrors the production environment setup for final acceptance testing (UAT) and pre-release verification.
  * Receives promoted batches from `develop` via a Pull Request when preparing a candidate release.
  * Automatically deploys to the staging server/container.
- **`develop` (Development / Integration):**
  * Active integration branch for day-to-day engineering.
  * All feature (`feat/*`), bug fix (`fix/*`), refactor (`refactor/*`), and maintenance (`chore/*`) branches branch off `develop` and target `develop` in their PRs.

---

## 2. Branch Naming Rules

Create your working branch off the latest `develop` branch (or `main` only if it is an emergency production hotfix).

**Format:** `<type>/<short-kebab-case-description>`

- Use lowercase letters, numbers, and hyphens (`-`) only.
- Avoid generic names like `feat/update` or `fix/bug`.
- Keep names concise (2–4 words).

| Prefix | Purpose | Branch From | Target PR Into | Example |
| :--- | :--- | :--- | :--- | :--- |
| `feat/` | New feature or capability | `develop` | `develop` | `feat/jwt-auth-guard` |
| `fix/` | Bug fix in development | `develop` | `develop` | `fix/redis-ttl-mismatch` |
| `refactor/` | Structural code improvement | `develop` | `develop` | `refactor/database-queries` |
| `chore/` | Tooling, dependencies, Docker | `develop` | `develop` | `chore/update-dockerfile` |
| `docs/` | Documentation only | `develop` | `develop` | `docs/api-specs` |
| `hotfix/` | Critical fix for live production | `main` | `main` (and back-merge to `develop` & `staging`) | `hotfix/login-crash` |

---

## 3. Commit Message Convention

We strictly follow the **Conventional Commits** standard.

### Format
```text
<type>(<scope>): <short imperative description>

[optional body: explain WHY this change was made]

[optional footer(s): BREAKING CHANGE: <details>, or Refs #issue]
```

#### Key Formatting Rules
- **Subject line length:** Keep the first line under **72 characters**.
- **Punctuation:** Do **not** end the subject line with a period.
- **Imperative mood:** Use present-tense commands (*"add"*, *"fix"*, *"refactor"*), never past-tense (*"added"*, *"fixed"*).
  * *Tip:* Complete this sentence: *"If applied, this commit will **[your subject line]**"*.

### Commit Types
| Type | When to Use | SemVer Impact |
| :--- | :--- | :--- |
| **`feat`** | A new user-facing feature or API capability | **MINOR** (`1.1.0` → `1.2.0`) |
| **`fix`** | A bug fix or patch | **PATCH** (`1.1.0` → `1.1.1`) |
| **`perf`** | Performance improvements (queries, caching, memory) | **PATCH** (`1.1.0` → `1.1.1`) |
| **`refactor`** | Code changes that neither fix a bug nor add a feature | None |
| **`chore`** | Updating configs, dependencies, Dockerfiles, or scripts | None |
| **`docs`** | Documentation or README changes only | None |
| **`test`** | Adding or correcting unit/integration tests | None |
| **`ci`** | Updates to CI/CD workflows, runners, or deploy hooks | None |

### Scopes
The `scope` identifies the module, service, package, or layer being modified. Always wrap the scope in parentheses.

> **Note on Scope Flexibility:**  
> The scopes below represent **common baseline examples, not a closed or exhaustive list**. You are welcome and encouraged to introduce new, descriptive scopes (e.g., `(billing)`, `(notification)`, `(scanner)`, `(ui)`) whenever working on distinct domain services, features, or architectural layers.

### Common Example Scopes
- `(auth)` — Authentication, JWT, guards, permissions, session management
- `(api)` — Endpoints, GraphQL resolvers, controllers, DTOs
- `(db)` — Migrations, entities, seeds, ORM schema changes
- `(docker)` — Dockerfiles, compose stacks, container health checks
- `(config)` — Environment variables, global configuration constants
- `(deps)` — Dependency updates and package additions

*(If a change affects the entire repository globally or has no single distinct boundary, you may omit the scope entirely).*

### Breaking Changes
If a change breaks backward compatibility (e.g., modifying an existing API response format, removing a required database column, or adding an essential environment variable):

1. Place an exclamation mark (`!`) immediately after the type/scope.
2. Add a `BREAKING CHANGE:` description in the commit footer.

```text
feat(auth)!: migrate jwt signing algorithm from HS256 to RS256

BREAKING CHANGE: Services must now mount RSA public keys in /certs
and configure the JWT_PUBLIC_KEY_PATH environment variable.
```

---

## 4. Development & PR Workflow

Follow these steps for everyday feature and bug fix tasks:

### Step 1: Update your local `develop`
```bash
git checkout develop
git pull origin develop
```

### Step 2: Create a task branch
```bash
git checkout -b feat/user-profile-avatar
```

### Step 3: Make changes & commit
```bash
git add .
git commit -m "feat(user): add profile avatar upload endpoint"
```

### Step 4: Rebase onto latest `develop` before pushing
Before pushing or opening a PR, always rebase to avoid merge conflicts:
```bash
git fetch origin
git rebase origin/develop
```

### Step 5: Push and open a Pull Request
Push your branch to the remote repository:
```bash
git push -u origin feat/user-profile-avatar
```
- Open a PR targeting **`develop`**.
- Fill out the PR template with clear context, testing steps, and infrastructure impacts (DB migrations, `.env` additions).
- Request a review from at least one collaborator.

### Step 6: Merging
- Once approved and CI checks pass, use **Squash and Merge** into `develop`.
- Delete the remote task branch after merging to keep the repository tidy.

---

## 5. Staging Promotion & Production Release

### Staging Promotion (UAT Candidate)
When a batch of work on `develop` is complete and ready for end-to-end verification:
1. Open a Pull Request from **`develop`** into **`staging`** titled `chore(staging): promote develop to staging`.
2. Merge using a standard merge commit (`--no-ff`) to preserve release history.
3. CI/CD deploys the code automatically to the staging environment for QA/UAT.

### Production Release
Once verified on staging:
1. Open a Release PR from **`staging`** into **`main`** titled `chore(release): vX.Y.Z`.
2. Merge into `main` using a standard merge commit (`--no-ff`).
3. Create and push an annotated Git Tag:
   ```bash
   git checkout main
   git pull origin main
   git tag -a v1.1.0 -m "Release v1.1.0: Add user avatars and auth guards"
   git push origin v1.1.0
   ```
4. CI/CD triggers on the new tag, builds production containers, applies migrations, and deploys.

---

## 6. Emergency Hotfixes

If a critical bug occurs directly on production:

1. Create a branch directly from `main`:
   ```bash
   git checkout -b hotfix/auth-token-expiry main
   ```
2. Commit the fix:
   ```bash
   git commit -m "fix(auth): correct token expiry calculation in payload"
   ```
3. Open a PR targeting **`main`**.
4. Once merged, create a patch tag (e.g., `v1.1.1`) and deploy:
   ```bash
   git checkout main
   git pull origin main
   git tag -a v1.1.1 -m "Hotfix v1.1.1: Fix token expiry calculation"
   git push origin v1.1.1
   ```
5. **Back-merge immediately to both `staging` and `develop`** so changes are not overwritten in subsequent releases:
   ```bash
   # Back-merge to staging
   git checkout staging
   git pull origin staging
   git merge main
   git push origin staging

   # Back-merge to develop
   git checkout develop
   git pull origin develop
   git merge main
   git push origin develop
   ```