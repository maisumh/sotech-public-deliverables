# GitHub Organization Migration Plan

Issue: [SOT-1689](/SOT/issues/SOT-1689)  
Date: 2026-07-06  
Owner: Operations Lead  
Status: Plan only. No repository transfers or production integrations changed.

## Executive Recommendation

Move active SoTech operational and client repositories out of personal GitHub
accounts into a company-owned GitHub organization, but do it in staged waves
with an explicit approval gate before any transfer.

Recommended target owner: a new or confirmed SoTech-controlled organization with
an unambiguous slug such as `the-social-technologies`, `sotech-agency`, or
`sotech-digital`. Do not use `sotech` unless Maisum already owns/control it
through another channel; public GitHub search shows `github.com/sotech` is an
unrelated GitHub user with existing public repositories.

The first approved execution wave should transfer internal SoTech repos and one
low-risk non-production client/support repo, verify GitHub App + Vercel behavior,
then continue with production-connected website/platform repos.

## Current Inventory

### Active Client Website Registry

Source: `/paperclip/workingdirectory/repo-registry.json`.

| Client | Current repo | Production domain | Framework | Migration priority |
|---|---|---|---|---|
| 180 AgPros | `maisumh/sotech-180agpros-website` | `https://www.180agpros.com` | Next.js | Wave 2 |
| CVCHÉ | `maisumh/cvche-website` | `cvche.kitchen` | Next.js | Wave 2 |
| Garden Montessori Schools | `abbas1hasan1/gms-website` | `www.gardenmontessorischools.com` | Next.js | Wave 3, cross-owner |
| Revive Renovation & Construction | `abbas1hasan1/revive-website` | not set in registry | Next.js | Wave 3, cross-owner |
| Solar City Car Wash | `maisumh/sotech-sccw-website` | not set in registry | Next.js | Wave 2 |
| SoTech | `maisumh/sotech-website` | `thesocialtech.net` | Next.js | Wave 2, own site |
| Tribes | `maisumh/sotech-tribes-website` | `trytribes.com` | Next.js | Wave 2 |
| WashMaxx | `maisumh/sotech-washmaxx-website` | `gowashmaxx.com` | Next.js | Wave 2, high SEO sensitivity |

No GitHub repo is listed for Nordam, Placement House, Shabber Daycares, or ZO
Skin Health in the registry.

### Active SoTech Operational Repos Found Through GitHub App Search

These are the repos that appear operationally relevant and should be considered
in scope unless Maisum excludes them:

| Repo | Purpose / notes | Recommended wave |
|---|---|---|
| `maisumh/sotech-brain` | Knowledge base/source of truth. Multiple agents reference this exact owner/repo. | Wave 1, after app permissions are confirmed |
| `maisumh/sotech-agents-platform` | Paperclip/back-office platform. High-impact core system. | Wave 2 or 3 |
| `maisumh/sotech-platform` | Client portal/admin app at `platform.thesocialtech.net`. Production-connected. | Wave 2 or 3 |
| `maisumh/sotech-paperclip-workingdirectory` | Shared agent working directory repo. | Wave 1 |
| `maisumh/sotech-hermes-agent` | Hermes operational agent. | Wave 2 |
| `maisumh/sotech-voice-agent` | Maya/front-office voice agent. | Wave 2 |
| `maisumh/sotech-blog-agent` | Blog worker. | Wave 2 |
| `maisumh/sotech-social-agent` | Social worker. | Wave 2 |
| `maisumh/sotech-brain-agent` | Legacy/brain automation agent. | Wave 2 |
| `maisumh/sotech-brain-mcp` | Brain remote MCP server. | Wave 2 |
| `maisumh/sotech-map-extended-mcp` | Extended Map/GHL MCP server. | Wave 1 or 2 |
| `maisumh/sotech-website-template` | Website starter template used by build workflows. | Wave 1 |
| `maisumh/sotech-tribes-proposal-templates` | Proposal templates. | Wave 1 |
| `maisumh/sotech-client-health-dashboard-website` | Internal dashboard site. | Wave 1 |
| `maisumh/sotech-finance-dashboard` | Internal finance dashboard. | Wave 1 |
| `maisumh/cvche-voiceagent`, `maisumh/cvche-voiceagent-livekit`, `maisumh/cvche-finance` | Client-adjacent tools. | Wave 2 after owner decision |

GitHub search also shows many old personal/test repos under `maisumh/*`
(`cookie`, `ecommerce`, political/test projects, old demos, etc.). I recommend
excluding those from the SoTech organization migration unless Maisum explicitly
classifies them as business assets.

### Local Clone / Config References Found

| Location | Reference type | Update needed after transfer |
|---|---|---|
| `/paperclip/workingdirectory/repo-registry.json` | Active client repo mapping | Replace `maisumh/*` and selected `abbas1hasan1/*` with new org owner/repo slugs after transfer. |
| `agents/*/TOOLS.md` and agent memory/docs | Operational docs mention `maisumh/sotech-brain` and client repos | Update live instructions that drive future agent routing; leave historical memory alone unless it is used as config. |
| `agents/founding-engineer/AGENTS.md` | Template clone command uses `https://github.com/maisumh/sotech-website-template.git` | Update template owner after transfer. |
| `builds/sotech-platform/site/apps/portal/.env.example` | `GITHUB_ORG`, `GITHUB_TEMPLATE_OWNER`, `GITHUB_TEMPLATE_REPO` | Update defaults/examples to target org/template. |
| `builds/sotech-platform/site/apps/portal/docs/github-app-setup.md` | GitHub App setup docs mention `GITHUB_ORG=sotech-client-blogs` | Reconcile with actual target org and production env. |
| `builds/sotech-platform/site/apps/portal/src/app/api/tenants/[id]/github/route.ts` | Creates repos using `process.env.GITHUB_ORG` and template owner/repo | Production env must be changed only after the GitHub App is installed on the new org. |
| `builds/sotech-platform/site/apps/portal/src/lib/github/app.ts` | Uses default `GITHUB_ORG` installation and owner-specific installations | Must verify installation lookup for both old owner and new org during transition. |
| Platform DB `platform.tenants.github_repo_owner/name/url` | Runtime source of truth for publishing and repo page reads | Needs a controlled data update after each repo transfer. |
| Vercel projects | Git integration links to old GitHub owner/repo | Must verify or reconnect each project under Project Settings -> Git after transfer. |
| Local clones in `builds/`, `proposals/`, `tmp/` | `origin` remotes | Update with `git remote set-url origin https://github.com/{new-org}/{repo}.git` only for active clones. |

## Target Organization Setup

Before transfers, Maisum should approve/create the target org and assign
ownership:

1. Create or confirm a GitHub organization controlled by The Social Technologies
   LLC.
2. Require 2FA for all org members.
3. Create teams: `owners`, `engineering-admin`, `engineering-write`,
   `seo-analyst`, `ops-read`, and `automation-bots`.
4. Set org base permission to `No permission` unless Maisum explicitly wants
   broad internal visibility.
5. Install required GitHub Apps on the org before transfer execution: Vercel,
   SoTech Platform GitHub App, and any Paperclip/Codex/GitHub app used by agent
   runtime.
6. Confirm billing plan. GitHub warns that private repos transferred to a Free
   org can lose protected branches / GitHub Pages features, so the target org
   plan must support our required protections.

## Migration Waves

### Wave 0: Approval and Backups

- Confirm target org slug and admins.
- Confirm included repo list and exclusions.
- Export a point-in-time inventory for each repo: visibility, default branch,
  branch protection/rulesets, Actions secrets/variables names, deploy keys,
  webhooks, GitHub App installations, collaborators/teams, environments, Pages,
  packages, and Vercel project link.
- Freeze production-impacting deploy changes during each transfer window.
- Notify internal team that old `maisumh/*` URLs should redirect temporarily but
  new remotes must be adopted.

### Wave 1: Low-Risk Internal/Templates

Transfer first after Wave 0 is approved: `sotech-website-template`,
`sotech-tribes-proposal-templates`, `sotech-paperclip-workingdirectory`,
`sotech-client-health-dashboard-website`, `sotech-finance-dashboard`, and
possibly `sotech-map-extended-mcp`.

Validation: clone from new org, push a no-op/test branch, confirm Vercel
projects still see pushes or reconnect if needed, and update docs/template clone
commands.

### Wave 2: Production-Connected SoTech + Client Repos Under `maisumh`

Transfer in small batches, one repo/project at a time:

- SoTech internal: `sotech-platform`, `sotech-agents-platform`,
  `sotech-hermes-agent`, `sotech-voice-agent`, `sotech-blog-agent`,
  `sotech-social-agent`, `sotech-brain`, `sotech-brain-mcp`,
  `sotech-brain-agent`.
- Client websites in `repo-registry.json` under `maisumh`: 180 AgPros, CVCHÉ,
  SCCW, SoTech website, Tribes, WashMaxx.

Validation after each repo: old URL redirect, new clone/fetch/push, open PRs,
Actions, Vercel preview trigger, Platform tenant repo fields, Paperclip/Senior
Developer registry lookup.

### Wave 3: Cross-Owner Repos Under `abbas1hasan1`

These need Abbas/Maisum coordination and must not be assumed movable by this
agent: `abbas1hasan1/gms-website`, `abbas1hasan1/revive-website`, and any
referenced proposal/template repos under `abbas1hasan1/*`.

### Wave 4: Archived / Proposal / Legacy Repos

After active systems are stable, decide whether to move private proposal repos
and archives. Recommendation: move only active proposal templates and current
client deliverables first. Archived one-off proposal repos can stay under the
original owner until there is a cleanup project.

## Code and Config Update Checklist

After each approved transfer wave, update:

- `repo-registry.json` active repo slugs.
- Brain client profiles: `01-knowledge/clients/{slug}.md` `system_ids.github`
  fields.
- Brain tool docs and Operations/Senior Developer/SEO/QA instructions that
  hardcode old repo owners.
- Platform env vars: `GITHUB_ORG`, `GITHUB_TEMPLATE_OWNER`, possibly
  `GITHUB_TEMPLATE_REPO`.
- Platform database tenant rows: `github_repo_url`, `github_repo_owner`,
  `github_repo_name`.
- Vercel project Git connections and GitHub App repository access selections.
- GitHub App installation IDs/secrets if the Platform app currently uses a
  single owner installation.
- GitHub Actions repository/org secrets and variables audit. GitHub says repo
  webhooks, services, secrets, and deploy keys remain associated after transfer,
  but verify each production deploy secret exists and is readable.
- Webhooks and deploy keys audit. Remove stale personal-account deploy keys and
  confirm no key grants write access unnecessarily.
- README/package metadata links, `repository` fields in `package.json`, badges,
  docs, screenshots, onboarding docs, and PR templates.
- Local active clone remotes using `git remote set-url origin NEW_URL`.
- Any submodules: `.gitmodules`, lockfiles, docs references.
- Any package registries/packages linked to old owner/repo.

## Risks and Mitigations

| Risk | Why it matters | Mitigation |
|---|---|---|
| Wrong target org | `sotech` is already an unrelated GitHub user. | Confirm/create an unambiguous org slug before doing anything. |
| Vercel stops deploying | Vercel requires GitHub org/repo permissions and may need Project Settings -> Git reconnect. | Install/configure Vercel GitHub App for target org first; test with Wave 1. |
| GitHub App permissions break Platform publishing | Platform uses `GITHUB_ORG`, installation lookup, tenant `github_repo_owner/name`, and GitHub App installation access. | Install app on new org before transfer; run platform GitHub health checks; update tenant rows only after verified. |
| Branch protection/rulesets lost or unavailable | GitHub warns private repos moved to Free orgs can lose protected branches/GitHub Pages. | Confirm target org plan and export/recreate rulesets. |
| Secrets/webhooks/deploy keys behave unexpectedly | GitHub says they remain associated, but external services may still depend on owner/repo identity. | Inventory names before transfer, verify after transfer, rotate risky deploy keys. |
| Old URL redirect dependence | GitHub redirects old repo URLs, but redirects are deleted if someone creates a repo/fork at the old location. | Update all configs/remotes to new URLs; do not recreate repos under the old names. |
| GitHub Pages/custom domains | GitHub docs say repository links redirect, but GitHub Pages sites do not redirect the same way. | Check Pages usage before each transfer. |
| Cross-owner access | Abbas-owned repos require separate permissions and acceptance. | Treat `abbas1hasan1/*` as Wave 3 with explicit Abbas/Maisum coordination. |
| Agent routing breaks | Senior Developer/SEO workflows read `repo-registry.json` and instructions. | Update registry and agent docs in the same change set as each production repo wave. |

## External Documentation Checked

- GitHub Docs: repository transfers preserve issues, PRs, webhooks, services,
  secrets, deploy keys, and git history; old repo links redirect; local remotes
  should still be updated; old redirects are permanently deleted if the old repo
  path is recreated.
  https://docs.github.com/en/repositories/creating-and-managing-repositories/transferring-a-repository
- GitHub Docs: organization repo roles define least-privilege access; Admin is
  required for sensitive/destructive settings and transfer management.
  https://docs.github.com/en/organizations/managing-user-access-to-your-organizations-repositories/managing-repository-roles/repository-roles-for-an-organization
- Vercel Docs: existing projects can change/disconnect Git repositories from
  Project Settings -> Git; GitHub organization repos require org owner or member
  repository access. https://vercel.com/docs/git/vercel-for-github
- Vercel KB: missing repos in Vercel usually require configuring GitHub App
  repository access/permissions.
  https://vercel.com/kb/guide/unable-to-find-github-repository

## Approval Request

Approve Wave 0 only, not repo transfers yet.

If approved, the next action is to create execution child tasks for:

1. Confirm/create target GitHub org slug and owners.
2. Export detailed per-repo inventory for Wave 1 + Wave 2 candidates.
3. Verify Vercel and SoTech Platform GitHub App installation access for the
   target org.
4. Produce a final wave-by-wave transfer runbook with exact repo order and
   rollback/verification steps.

No transfer, production env change, Vercel Git reconnect, or Platform database
update should happen until that Wave 0 inventory/runbook is reviewed and
approved.
