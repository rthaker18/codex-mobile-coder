# Blueprint: Codex-driven website changes from phone -> staging -> production

This is a practical blueprint you can apply to most web stacks (Next.js, React/Vite, Rails, Django, etc.).

## 1) System architecture

- **Repository host:** GitHub
- **Bot trigger surface:** GitHub Issue comments (mobile-friendly)
- **AI operator:** Codex agent with scoped repo access
- **Staging deploy:** Automatic on merge to `main`
- **Production deploy:** Manual approval gate + explicit deploy command

### Why this architecture works on phone

- You can post issue comments from the GitHub mobile app.
- Commands become auditable messages (who asked what, when).
- Every code change still goes through PR + CI checks.

---

## 2) Branch and environment strategy

- `main` -> deploys to **staging**
- `prod` (or release tag) -> deploys to **production**

Recommended environment protections:

- **staging environment**
  - no required reviewers
- **production environment**
  - required reviewers enabled
  - optional wait timer

This keeps production deployment explicit and reviewable.

---

## 3) Chat command contract

Use GitHub issue comments as commands. Example contract:

### A) Request a code change

```text
/change
Title: Update homepage hero
Task: Change CTA button text from "Start" to "Get Started"
Files (optional): src/pages/index.tsx
Acceptance criteria:
- New text appears on staging hero button
- Existing tests pass
```

Expected automation behavior:

1. Bot validates the command format.
2. Bot opens/updates a working branch.
3. Codex applies change.
4. Bot opens PR with summary + test results.

### B) Promote to production

```text
/deploy prod
Source: main
Reason: validated on staging from iPhone
```

Expected behavior:

1. Bot triggers production workflow.
2. GitHub environment requires human approval.
3. Deployment runs only after approval.

---

## 4) CI/CD flow (end-to-end)

1. `/change` command posted from phone.
2. Codex generates commit(s) and opens PR.
3. `ci.yml` runs checks on PR.
4. Merge PR into `main`.
5. `deploy-staging.yml` deploys staging.
6. You validate staging from phone.
7. `/deploy prod` command posted.
8. `deploy-prod.yml` waits for production approval.
9. Approved deployment publishes production.

---


## Public repository guidance

Yes, this model can be run in a public repository, but only with strict controls:

- Enforce single-operator mode: set repository variable `ALLOWED_OPERATOR` to your GitHub username and only allow that account to run commands.
- Also require trusted author association (`OWNER`, `MEMBER`, `COLLABORATOR`) for that account.
- Keep `/deploy prod` behind protected `production` environment approvals.
- Never store deployment credentials in the repo; use environment secrets only.
- Prefer fine-grained tokens and avoid broad `contents: write` unless required.
- Keep branch protection enabled for `main`/`prod`.

---

## 4.1) Single-operator lock (required for your use case)

Because you want a public portfolio but private edit authority, lock command execution to your account:

- In **Settings -> Secrets and variables -> Actions -> Variables**, add:
  - `ALLOWED_OPERATOR=<your_github_username>`
- In `codex-command-router.yml`, authorize only when:
  - commenter login equals `ALLOWED_OPERATOR`, and
  - author association is trusted.

This means recruiters can view your repo/site, but cannot trigger `/change` or `/deploy prod`.

---

## 5) Security and guardrails (must-have)

- Use least-privilege token for automation.
- Disallow direct push to `main` and `prod`.
- Require PR checks before merge.
- Require production environment reviewers.
- Keep audit logs for all bot-triggered actions.
- Add a kill switch label/flag to disable bot commands quickly.

---

## 6) What you need to customize

In workflow templates, replace placeholders for:

- package manager (`npm`, `pnpm`, `yarn`, etc.)
- build/test commands
- hosting deploy commands (Vercel/Netlify/AWS/GCP/etc.)
- secrets (`STAGING_DEPLOY_TOKEN`, `PROD_DEPLOY_TOKEN`, etc.)

---

## 7) Validation checklist

- [ ] PR to `main` runs CI successfully.
- [ ] Merged PR deploys staging.
- [ ] Staging URL updates with new change.
- [ ] `/deploy prod` command triggers deploy workflow.
- [ ] Production approval is required before deployment.
- [ ] Production URL reflects approved release.

---

## 8.1) Recruiter showcase checklist

- [ ] Repository is public for visibility.
- [ ] `ALLOWED_OPERATOR` is set to your username.
- [ ] A non-owner account cannot trigger command workflows.
- [ ] A successful demo run is documented (command comment, PR, staging URL, prod deploy approval).

---

## 8) Optional enhancements

- Preview URLs per PR.
- Auto-screenshot + visual diff checks.
- Rollback command (`/rollback prod <release-id>`).
- Required test tags for risky files (auth, billing, data migration).

