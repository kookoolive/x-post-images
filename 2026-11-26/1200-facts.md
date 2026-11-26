# 2026-11-26 12:00 facts — Claude Ship

## Sources (fetched 2026-11-26)
- https://claude.com/resources/articles/introducing-claude-ship
- https://claude.com/product/claude-ship
- https://code.claude.com/docs/en/ship-command
- https://claude.dev/blog/the-work-after-you-open-a-pr/ (Addy Osmani, 2026-11-24; supporting context only; headline claims from official product/docs)
- Official post: https://x.com/ClaudeDevs/status/2124959436640858475 (2026-11-23 20:13 UTC)

## Product
- Announced 2026-11-23. Claude Ship is in beta for Claude Team and Enterprise, GitHub repositories only.
- Three agents: Code Review (generally available) + PR Steward (beta) + Claude Approvals (beta).
- Not available to orgs with Zero Data Retention, the HIPAA configuration, or customer-managed encryption keys.
- Usage billed on tokens. Admins can set spend limits for Code Review and PR Steward. Claude Approvals has no base charge, but classifiers in a policy are billed on tokens they use; a policy that uses Claude's review triggers a billed Code Review, even in observe mode. Running /ship counts as regular Claude Code usage.

## Code Review
- In-depth review through multiple lenses; collects findings, adversarially verifies them, categorizes them, posts to the PR.
- Findings ranked Important, Nit, or Pre-existing (product page).
- Review levels per repo: Quick, Standard, or Deep. Can be tuned with REVIEW.md.
- On the Claude Code team, where Claude's review is one layer of a broader process, merged PRs more than doubled from June to September while the share reverted fell by more than 40%.

## PR Steward (beta)
- Works to keep a PR green; designed not to approve or merge pull requests (article). Product page: "designed to leave approving and merging to your team."
- Jumps in on failing CI checks, merge conflicts, and review feedback from Code Review, other review tools, or team members.
- After every push, comments with what it changed and how to undo it. Developers can comment @claude to steer, or @claude stop to have it step back.
- Admin turns it on per repository. Product page recommends starting in Manual mode then turning on by default once trusted.

## Claude Approvals (beta)
- Applies an approval policy the team writes as a file in the repository. Starts when a connected repo has a policy file on its default branch; an admin can turn it off.
- Can approve routine changes if the policy allows, or require human sign-off.
- Official recommendation: enable first in observe-only mode (posts its decision but approves nothing) before configuring branch protection to require Claude Approvals' green light.

## /ship and /steward (Claude Code, local) are not Claude Ship (org cloud product)
- /ship and /steward run from the Claude Code CLI in a local clone. Cloud sessions don't have them. Need Claude Code v2.1.330 or later.
- After /ship: can commit, push, open PR as you; reviews with /code-review; watch from your session; once ready, merge it yourself.
- /steward skips opening/review and starts the watch.
- Uses your gh and git sign-ins; work on GitHub appears under your name. What can merge depends on permissions and branch rules.
- /steward is separate from PR Steward. Where PR Steward is already working on the PR, no local watch starts.
- bypassPermissions mode: no watch starts. Native Windows: no watch starts (PR can open and be reviewed). Public repo: /ship doesn't commit uncommitted changes.
- Warning: run a watch only where you trust everyone who can merge to the base branch or push to your branch. Any of them can run programs on your computer with your credentials.

## Angle (avoid overlap with 2026-11-17 12:00 checks-out-of-reach)
- That post was about putting verification rules on a branch the agent cannot modify.
- This post: review / fix / approve are three agents; the one that turns PRs green is designed not to approve or merge; local /ship is not the org Claude Ship product.
