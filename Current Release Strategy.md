# Current Strategy

This is the current release strategy for our 6 apps in GitHub.

## Weekly Deploy

Each week, we create a new tag off our main branch, with the format of `release-{date}`. This triggers a GitHub Actions deploy workflow,
which builds the app and deploys to an Azure Webapp. We do our own testing in a Webapp Deployment
Slot, and then attempt to release every Thursday evening at 6pm CST. If it doesn't work, we have a `rollback` GH Actions workflow that
rolls it back to the last good `release` tag.

## Existing Pain Points

- the use of tags for deploys has created a bloat of tags. Would also like to avoid branch bloat as well
- No automated testing strategy
- No way to confirm across 4 team members what configuration/environment variables need to be set or changed

## What is up for change

- The use of tags for deployments, because we have experienced a significant bloat of tags.
- Testing strategy
- The time of release is not set in stone, though would prefer to maintain after hours but not late into the night, as most of our users are not up that late.
- The branch we deploy from

## What is not up for change

- our use of Webapp Deployment Slots
- our use of GitHub Actions as the form of deploy (no need to find replacements for GH Actions)

## What is in process

- Currently working on adding a suite of tests with Playwright to check if certain main functions of our site work as intended
