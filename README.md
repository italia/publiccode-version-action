# publiccode.yml softwareVersion check action for GitHub

[![Join the #publiccode channel](https://img.shields.io/badge/Slack%20channel-%23publiccode-blue.svg?logo=slack)](https://developersitalia.slack.com/messages/CAM3F785T)
[![Get invited](https://slack.developers.italia.it/badge.svg)](https://slack.developers.italia.it/)

This action checks whether `softwareVersion` and `releaseDate` in your 
[publiccode.yml file](https://github.com/italia/publiccode.yml) is 
aligned with the latest tag. It's also ready to work in conjunction with
`peter-evans/create-pull-request` action to update fields in `publiccode.yml` 
using a pull request. You can also specify a remote repository, see 
[Deal with a remote repository](#deal-with-a-remote-repository)

Click `Use this template` button to create your action based on this template.

## Inputs

The following inputs briefly explained here are fully declared and documented in the [action.yaml](action.yaml):

* `remoterepo` [**Optional**] - Git url of the remote repository you want to check (default `''`)

* `publiccode` [**Optional**] - publiccode.yml path (default `publiccode.yml`)

* `remove` [**Optional**] - Regular expression applied to the latest tag to remove characters (default `''`)

* `filter` [**Optional**] - Regular expression applied to filter tags (default `''`)

* `gitname` [**Optional**] - Git name configuration for bump commit if you use a Pull Request action (default `Publiccode bot`)

* `gitmail` [**Optional**] - Git mail configuration for bump commit if you use a Pull Request action (default `''`)

## Examples

Include this action in your repo by creating 
`.github/workflows/publiccode-versioning.yml` and edit where needed:

```yml
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Parse publiccode.yml
        uses: italia/publiccode-softwareversion-check-action@v1.1.0
```

You can easily create a pull request to update fields in your `publiccode.yml` 
file.

```yml
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Parse publiccode.yml
        uses: italia/publiccode-softwareversion-check-action
        id: pva
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v3
        if: failure()
        with:
          title: "feat: update softwareVersion ${{ steps.pva.outputs.version }} in publiccode.yml"
          branch: feature/publiccode-${{ steps.pva.outputs.version }}
```

See ["Create Pull Request - GitHub action" documentation](https://github.com/marketplace/actions/create-pull-request) 
for more info

### Deal with a remote repository

If you need to keep your `publiccode.yml` in another repository you can still 
fetch that repo directly from the action using `remoterepo` parameter and use
a cron to check whether the version is changed periodically.

```yml
on:
  schedule:
    - cron:  '30 17 * * *'

  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: publiccode-softwareversion-check-action
        uses: italia/publiccode-softwareversion-check-action@master
        id: pva
        with:
          remoterepo: "https://github.com/immuni-app/immuni-app-android.git"
```

Tags may contain extra characters you don't want to put in your `SoftwareVersion`. 
In that case you can remove unwanted characters using a regular expression in the
`remove` parameter.

```yml
  remove: "Immuni-|build[0-9]+"
```

## Build the action

Install dependencies

```sh
npm i
```

Build the action

```sh
npm run build
```

## Contributing

Contributing is always appreciated.
Feel free to open issues, fork or submit a Pull Request.
If you want to know more about how to add new fields, check out [CONTRIBUTING.md](CONTRIBUTING.md).
In order to support other country-specific extensions in addition to Italy some
refactoring might be needed.

## Maintainers

This software is maintained by the
[Developers Italia](https://developers.italia.it/) team.

## License

© 2021 Dipartimento per la Trasformazione Digitale - Presidenza del Consiglio dei
Ministri

Licensed under the EUPL.
The version control system provides attribution for specific lines of code.

## Remarks

This GitHub Action is published in the GitHub Marketplace.
As such, you can find the [Terms of Service here](https://docs.github.com/en/free-pro-team@latest/github/site-policy/github-marketplace-terms-of-service).
Also, [here](https://docs.github.com/en/free-pro-team@latest/github/site-policy/github-marketplace-developer-agreement)
you can find the GitHub Marketplace Developer Agreement.
