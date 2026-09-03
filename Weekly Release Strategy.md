This is the weekly release strategy for StraightDocs, Hive, and all the
other apps we deploy on a weekly basis.

## Timeline

### Friday - Wednesday, 11:59am CST

`main` is gated and only accepts PRs, no immediate writes. On creation
of every PR, an automated `build & test` GitHub Action is run. On every
successful merge into main, a deploy to `staging` is run. A PR must
include a review from at least one other person and a Copilot review.
Only the Release Manager has the ability to merge into main.

### Wednesday, 12:00pm CST

All unmerged PRs are either merged or paused. We publish to UAT all the
latest changes that are going out on Thursday. This is the regular
cutoff for the weekly release, minus emergency hotfixes and negotiated
late changes. We receive feedback from the users and make any necessary
changes.

### Thursday, 6:00pm CST

An automated GitHub Action runs that performs two checks:
1. Do the configurations match the `prod` environment? Fail if not
2. Does the Playwright suite of essential functions pass? Fail if not
If both checks pass, then the swap is initiated.

### PR template

Each project will have a PR template, which requires three things:

1.  A quick description of the changes

2.  Any production environment variables/configurations that need to
    change for the release

3.  Does this require any schema changes?

### Weekly Release Teams Template

Hey JT Bates Group! We have a good release coming your way this week!

**Features**

- Feature 1

- Feature 2

- Feature 3

**Bugfixes**

- Bugfix 1

- Bugfix 2

- Bugfix 3

Thank you for your patience as we work together to make StraightDocs
better!

### Weekly UAT Testing Request Template

Hey \@JT Bates Group! We have a good release coming up this week, and we
want your help testing it! Head over
to [uat.straightdocs.com](https://uat.straightdocs.com/) and submit a
ticket if you find any issues with these changes:

- change 1

- change 2

- change 3

Thank you for helping us test the new features coming to you! Please
note that [uat.straightdocs.com](https://uat.straightdocs.com/) is not
using the same data as production, but an old set of data, so you won't
overwrite anything that you see
in [straightdocs.com](https://straightdocs.com/)! 
