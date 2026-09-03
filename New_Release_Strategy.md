# Weekly Release Strategy

This is the release strategy for StraightDocs, Hive, and the other apps we deploy weekly.

## Summary of what changes

| | Today | New |
|---|---|---|
| Deploy trigger | `release-{date}` tag off main | Merge to `main` → staging; scheduled swap to prod |
| Prod gate | Manual testing in a slot | Automated config check + Playwright, both pre-swap |
| Branch protection | None on main | PR-only, required checks, one non-author approval |
| "What's in prod?" | Read the tag list | The `production` branch |
| Tag purpose | Prod deploys (bloat) | UAT/Beta publishes and emergency prod fixes only |
| Config coordination | Verbal / nothing | Committed manifest, checked automatically before swap |

---

## Branch model

**`main` is the trunk.** All work lands here via PR. No direct commits, ever — including hotfixes.

Branch protection on `main` requires:

- At least one approving review from someone other than the author, so no one person is the sole knowledge-holder on a change
- A Copilot review (advisory — it does not count as the human approval)
- Required status checks green: build, `just test`
- Linear history, branch up to date before merge

---

## Weekly timeline

### Wednesday, 12:00pm CST — cutoff and UAT publish

`main` freezes. We tag and publish to UAT, then ask JT Bates Group to test (template below).

The freeze is not enforced, but a new PR must carry a `late-change-request` label. Applying that label requires sign-off from the Release Manager via a comment in the PR. 

### Thursday, 2:00pm CST - check for failures

Run an automated test that checks the configuration variables and Playwright suite so we can attempt to catch early.

### Thursday, 6:00pm CST — production release

A scheduled workflow requests the deploy and waits on approval from the Release Manager via a GitHub Environment reviewer gate. If unapproved, nothing ships and the run expires.
To skip a week (holidays, nothing worth shipping), cancel the run or decline the approval. No code change required.

### Friday morning — the real detection window

The Release Manager is on point Friday morning to hear user feedback and catch true breaks, and will delegate to others the work if necessary.

---

## Pipeline

### On every merge to `main`

Deploy to the **staging slot** on every merge. Staging is always the current trunk, and between Wednesday's freeze and Thursday's release it is the release candidate.

### Thursday release sequence

Run per app, but in this order overall:

1. **Config check**: a quick, cheap, no side effect workflow in the forge. If any app fails, either quickly fix or abort the deploy.
2. **Playwright smoke, against the staging slot URL.**
3. **Swap** staging → prod.
4. **Fast-forward `production`** to the released SHA.
5. **Post the release announcement** to Teams.

---

## The two gates

### 1. Configuration check

Each repo holds `deploy/required-settings.json` — the list of app settings and connection string names the current commit expects.
 The check compares that manifest against the **prod slot's** settings and fails if anything is missing.

- **Check the prod slot, not staging.** Slot settings must be marked as *deployment slot settings* (sticky) in Azure,
 or the swap will move staging's config into production. Audit this across all six apps before turning any of this on; it fails silently.

### 2. Playwright smoke suite

Small and fast — under three minutes, five to ten paths.

The full Playwright suite runs on PRs and against UAT, where it can take as long as it needs. Keep the two separate — a forty-minute suite cannot run at swap time and will get disabled the first time it delays a release.

---

## Production branch mechanics

`production` is written by automation only, in three places.

1. **Successful release** — fast-forward to the released SHA, as the *last* step after the swap is confirmed, never before.
2. **Emergency fix** — same, on the emergency deploy path.
3. **Rollback** — reset backwards to the previous SHA. This is a force-push, which is fine because only CI does it.

---

## Emergency fixes

1. Branch from `main`, PR into `main`. The Release Manager may self-approve during an incident, but a review is required.
2. Tag and deploy: `just tag prod-hotfix && git push --tags`.
3. Still runs the Playwright smoke test and configuration check
4. `production` branch updates automatically.

---

## Schema changes 

We are not moving to migration tooling yet. For now, schema changes must be declared in the PR.

---

## PR template

```markdown
## What changed


## Configuration
- [ ] No new or changed app settings
- [ ] Settings added/changed — `deploy/required-settings.json` updated in this PR

## Schema
- [ ] No schema change
- [ ] Schema change (paste SQL below, mark additive or destructive)

## Testing notes
Anything CI cannot verify. Do not paste `just test` output — it runs as a
required check and cannot be stale or copied from another branch.
```

---

## Roles

**Release Manager** — 

- Approving or declining late changes after noon on Wed.
- Approving the Thursday deployment gate, present at 6:00pm
- The Friday morning watch
- Rollback decisions

**Backup** — named at the same time, in case of illness or travel.

---

## Housekeeping

- **Tag retention.** Tags are now only for UAT, Beta, and emergency fixes, so bloat is much reduced — but UAT publishes weekly. A monthly pruning workflow keeps the last twelve per environment.
- **Release notes are generated, not written.** Build the Teams announcement from merged PR titles and labels so it cannot drift from what actually shipped.
- **Observability.** Define "broken" before Thursday: App Insights availability tests, an error-rate alert into Teams, and a dashboard the captain actually has open at 6:00pm. Otherwise "too broken" means "did anyone happen to notice."

---

## Communication templates

### Weekly release announcement

> Hey JT Bates Group! We have a good release coming your way this week!
>
> **Features**
> - Feature 1
> - Feature 2
> - Feature 3
>
> **Bugfixes**
> - Bugfix 1
> - Bugfix 2
> - Bugfix 3
>
> Thank you for your patience as we work together to make StraightDocs better!

### Weekly UAT testing request

> Hey @JT Bates Group! We have a good release coming up this week, and we want your help testing it! Head over to [uat.straightdocs.com](https://uat.straightdocs.com/) and submit a ticket if you find any issues with these changes:
>
> - change 1
> - change 2
> - change 3
>
> Thank you for helping us test the new features coming to you! Please note that [uat.straightdocs.com](https://uat.straightdocs.com/) is not using the same data as production, but an old set of data, so you won't overwrite anything that you see in [straightdocs.com](https://straightdocs.com/)!

### Rollback notice

Write this now, on a calm day. Not at 6:45pm.

> Hey JT Bates Group — we've rolled back this week's release after finding an issue with {area}. StraightDocs is back to the version you were using this morning, and nothing you've done today has been lost. We'll get the fix out {timeframe} and let you know when it's live. Sorry for the disruption, and thanks for your patience!

