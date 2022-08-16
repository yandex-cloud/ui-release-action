# ui-release-action GitHub action

## What is it?
Obviously, it is a [GitHub action](https://github.com/features/actions), it does the following things:
- when you push new commits into `master` branch (or any other branch you designate in `on.push.branches` field), 
  a release PR is created which includes an automatically generated CHANGELOG.md and bumped NPM version, all this is 
  done according to [conventional commits spec](https://www.conventionalcommits.org/en/v1.0.0/)
- in case any new code is merged into `master` while that release PR is still open, it will be either updated (if bump
  type stays the same) or recreated (if bump type changes)
- once the maintainer is ready to publish a new package version, he merges the release PR
- package unit tests are run (at least `"test": "exit 0"` should be defined in `package.json`)
- if they are successful, a new GitHub release is created and a new package version is published to NPM.

## How should I use it?

### Setting up
Create the file `.github/workflows/release.yml` at the root of your repo, providing at least the following inputs:
- `github-token` (who does create release PR)
- `npm-token` (who does publish NPM package)
- `node-version`, optional - which node version to use for running unit tests.

The file looks roughly like, you can change target branch, tokens and node version.
```yaml
on:
  push:
    branches: [master]

name: release

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: yandex-cloud/ui-release-action@main
        with:
          github-token: ${{ secrets.YC_UI_BOT_GITHUB_TOKEN }}
          npm-token: ${{ secrets.YC_UI_BOT_NPM_TOKEN }}
          node-version: 14
```

### Early development
The action encourages "moving fast and breaking things" by preventing breaking changes from bumping major version
in the early stages of project development (before you reach version 1.0.0). In other words, both the 
'feat: something' commits and the commits with 'BREAKING CHANGE: something' footer bump a minor component 
automatically. Once you consider your project stable enough, you should add `Release-As: 1.0.0` footer in one of
the commits and see the usual effect of the 'BREAKING CHANGE: something' footer on you major version.
