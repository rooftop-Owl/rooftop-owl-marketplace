# MARKETPLACE_DISPATCH_PAT — Setup & Rotation Runbook

## Purpose

Each companion plugin repo holds a `MARKETPLACE_DISPATCH_PAT` secret. When a push lands on
`main`, `notify-marketplace.yml` uses this PAT to fire a `repository_dispatch` event on
`rooftop-Owl/rooftop-owl-marketplace`. The marketplace `bump-pin.yml` workflow then opens
a PR to advance the SHA pin.

## Token Spec

| Field | Value |
|-------|-------|
| Type | Fine-grained PAT |
| Owner | rooftop-Owl (personal account) |
| Target repo | `rooftop-Owl/rooftop-owl-marketplace` ONLY |
| Permissions | `Contents: write`, `Metadata: read` |
| Expiry | 90 days |
| One token per | Plugin repo (do not share across repos) |

## Initial Setup

1. Go to GitHub → Settings → Developer settings → Fine-grained tokens → Generate new token
2. Set token name: `MARKETPLACE_DISPATCH_<PLUGIN>` (e.g. `MARKETPLACE_DISPATCH_ASTRAEA`)
3. Set expiration: 90 days
4. Repository access: **Only selected repositories** → `rooftop-Owl/rooftop-owl-marketplace`
5. Permissions: Repository permissions → Contents → **Read and write**; Metadata → **Read-only** (auto-selected)
6. Generate and copy the token
7. Go to the plugin repo → Settings → Secrets and variables → Actions → New repository secret
8. Name: `MARKETPLACE_DISPATCH_PAT`, Value: paste the token
9. Repeat for each of the 10 plugin repos (each gets its own token)

## Plugin Registry

| Plugin Repo | Plugin Name | Token Name |
|-------------|-------------|------------|
| rooftop-Owl/astraea | astraea | `MARKETPLACE_DISPATCH_ASTRAEA` |
| rooftop-Owl/skill-factory | skill-factory | `MARKETPLACE_DISPATCH_SKILL_FACTORY` |
| rooftop-Owl/aeolus-agents | aeolus-agents | `MARKETPLACE_DISPATCH_AEOLUS_AGENTS` |
| rooftop-Owl/dart-agents | dart-agents | `MARKETPLACE_DISPATCH_DART_AGENTS` |
| rooftop-Owl/climada-agents | climada-agents | `MARKETPLACE_DISPATCH_CLIMADA_AGENTS` |
| rooftop-Owl/wrf-chem-agents | wrf-chem-agents | `MARKETPLACE_DISPATCH_WRF_CHEM_AGENTS` |
| rooftop-Owl/research-zotero | research-zotero | `MARKETPLACE_DISPATCH_RESEARCH_ZOTERO` |
| rooftop-Owl/slideaway | slideaway | `MARKETPLACE_DISPATCH_SLIDEAWAY` |
| rooftop-Owl/notion-ao-research | notion-ao-research | `MARKETPLACE_DISPATCH_NOTION_AO_RESEARCH` |
| rooftop-Owl/ephemeris-plugin | ephemeris | `MARKETPLACE_DISPATCH_EPHEMERIS` |

## Rotation (every 90 days)

1. Generate a new fine-grained PAT with identical spec (see Initial Setup above)
2. Update the `MARKETPLACE_DISPATCH_PAT` secret in the plugin repo settings
3. Delete the old token from GitHub → Settings → Developer settings → Fine-grained tokens
4. Verify by merging any trivial commit to plugin main and confirming a bump PR appears on marketplace within 5 min
5. Repeat for each plugin repo

**Calendar reminder**: Set a recurring 85-day reminder so rotation happens before expiry.

## Compromise Response

If a PAT is suspected compromised:
1. Immediately delete the token from GitHub Settings
2. Audit recent `bump-pin.yml` runs for that plugin: Actions → bump-pin → filter by triggering repo
3. Revert any unexpected marketplace.json changes from that plugin's bump PRs
4. Generate a replacement token and update the secret
5. Notify: this affects only one plugin's dispatch capability; other plugins are unaffected

## Branch Protection (marketplace main)

Enable on `rooftop-Owl/rooftop-owl-marketplace`:
- Settings → Branches → Add rule → Branch name: `main`
- ✅ Require a pull request before merging
- ✅ Required approvals: 1
- ✅ Do not allow bypassing the above settings

This ensures `bump-pin.yml` can only land via PR review, not direct push.
