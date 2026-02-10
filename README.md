# Codex Mobile Website Ops Blueprint

This repository contains a concrete starter blueprint for running a website with a **Codex-assisted workflow**:

1. You open staging on your phone.
2. You ask Codex to make a change.
3. Codex creates a PR and, after merge, staging updates automatically.
4. You verify staging.
5. You ask Codex to deploy to production with an explicit approval gate.

See `docs/mobile-codex-blueprint.md` for the complete architecture and setup checklist.

## Included workflow templates

- `.github/workflows/ci.yml` — PR checks and lint/test placeholders.
- `.github/workflows/deploy-staging.yml` — auto-deploy staging from `main`.
- `.github/workflows/deploy-prod.yml` — manually approved production deployment.
- `.github/workflows/codex-command-router.yml` — issue-comment command router for `/change` and `/deploy prod` style commands.

## Public repository safety notes

This setup is safe for a public repository **if** you keep the guardrails enabled:

- Run in single-operator mode by setting repo variable `ALLOWED_OPERATOR` to your GitHub username.
- Restrict command execution to both your exact username and trusted commenter associations (`OWNER`, `MEMBER`, `COLLABORATOR`).
- Keep production deploys behind GitHub Environment required reviewers.
- Use least-privilege tokens and store them only in GitHub Secrets.
- Require PR checks and branch protection on deployment branches.

## Quick start

1. Copy these files into your real app repository.
2. Replace placeholder build/test/deploy commands.
3. Create GitHub environments (`staging`, `production`) and add required reviewers for `production`.
4. Add secrets used by your deploy platform.
5. Test the end-to-end flow using the checklist in `docs/mobile-codex-blueprint.md`.


## Recruiter demo mode (safe showcase)

To show this publicly without letting anyone else edit your site:

1. Keep repository public, but lock command execution to your username via `ALLOWED_OPERATOR`.
2. Use a dedicated demo issue (for command history) and perform edits from GitHub Mobile.
3. Keep production environment approvals enabled so every prod deploy is explicitly approved.
4. Add screenshots/GIFs of the command -> PR -> staging -> prod flow to your portfolio README.
