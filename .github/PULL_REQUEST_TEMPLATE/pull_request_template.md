## Description
<!-- Provide a brief summary of the changes introduced in this PR -->

Closes #<!-- Issue number, e.g., 123, [Optional if issue not exist] -->

---

## Target Branch / Merge To
<!-- Check the target branch for this PR to prevent accidental merges to protected environments -->
- [ ] `develop` — Features, bug fixes, refactors, and chore tasks
- [ ] `staging` — Release candidate promotion from `develop`
- [ ] `main` — Production release from `staging` or emergency `hotfix/*`

---

## Type of Change
<!-- Check the relevant option with an [x] -->
- [ ] `feat`: A new feature or capability
- [ ] `fix`: A bug fix or patch
- [ ] `perf`: Performance improvements
- [ ] `refactor`: Code restructuring without behavioral change
- [ ] `chore`: Tooling, configs, dependencies, Docker
- [ ] `docs`: Documentation updates only
- [ ] `test`: Unit / Integration tests
- [ ] `ci`: CI/CD runner or workflow adjustments

---

## Infrastructure & Configuration
- [ ] **Requires Database Migration:** `Yes / No`
- [ ] **Requires New Environment Variables (`.env`):** `Yes / No`
  <!-- If yes, list variable names and instructions here -->
- [ ] **Breaking Change:** `Yes / No`
  <!-- If yes, describe the required consumer migration path -->

---

## How to Test
<!-- Detail exact CLI commands or UI steps to verify these changes -->
1. Run automated tests:
   ```bash
   npm test
   ```
2. Manual verification steps:
   - Step 1: ...
   - Step 2: ...

---

## PR Checklist
- [ ] Branched off the latest `develop`
- [ ] PR targets the correct branch (`develop` for features/fixes, `staging` for promotion, `main` for release/hotfix)
- [ ] Rebased onto base branch to resolve conflicts
- [ ] Commits strictly follow Conventional Commits format
- [ ] No secrets, tokens, or local credentials committed