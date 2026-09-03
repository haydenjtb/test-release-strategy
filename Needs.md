# Needs

1. PR Template (.github/pull_request_template.md)
2. staging-deploy.yml (.github/workflows/staging-deploy.yml)
  a. deploys to staging when committing on main. checks all tests and playwright
3. full-playwright-suite.yml (.github/workflows/full-playwright-suite.yml)
  a. Runs the full Playwright Testing Suite (Thursdays at noon)
4. playwright-essentials.yml (.github/workflows/playwright-essentials.yml)
  a. Tests the playwright essentials
5. test-and-deploy.yml (.github/workflows/test-and-deploy.yml)
  a. run playwright essentials and configuration check before attempting to swap `staging` and `prod`.
6. Weekly_Release_Templates.docx (or .pdf) in the Teams chat
  a. The templates for **StraightDoc Weekly Update** and **StraightDocs Weekly Testing** teams updates
7. `late-change-request` and `late-change-approved` labels

## Configuration Check

create a bash script in the `bin/` directory of each project that checks the config and keyvault configuration names in prod
and compares against the local configurations.

## Playwright Tests

Mark some tests as **PlaywrightEssentials** and the rest as **Playwright**. Test Playwright Essentials with
`just playwright-essentials` which runs `dotnet test --filter "Category=PlaywrightEssentials"`. Test Playwright Full Suite
with `just playwright` which runs `dotnet test --filter "Category~Playwright"`
