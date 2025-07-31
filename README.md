# *chart-testing* Action

![](https://i.imgur.com/waxVImv.png)
### [View all Roadmaps](https://github.com/nholuongut/all-roadmaps) &nbsp;&middot;&nbsp; [Best Practices](https://github.com/nholuongut/all-roadmaps/blob/main/public/best-practices/) &nbsp;&middot;&nbsp; [Questions](https://www.linkedin.com/in/nholuong/)
<br/>

A GitHub Action for installing the [helm/chart-testing](https://github.com/nholuongut/chart-testing) CLI tool.

## Usage

### Pre-requisites

1. A GitHub repo containing a directory with your Helm charts (e.g: `charts`)
1. A workflow YAML file in your `.github/workflows` directory.
  An [example workflow](#example-workflow) is available below.
  For more information, reference the GitHub Help Documentation for [Creating a workflow file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file)

### Inputs

For more information on inputs, see the [API Documentation](https://developer.github.com/v3/repos/releases/#input)

- `version`: The chart-testing version to install (default: `3.11.0`)
- `yamllint_version`: The `yamllint` version to install (default: `1.33.0`)
- `yamale_version`: The `yamale` version to install (default: `4.0.4`)

### Example Workflow

Create a workflow (eg: `.github/workflows/lint-test.yaml`):

Note that Helm and Python must be installed.
This can be achieved using actions as shown in the example below.
Python is required because `ct lint` runs [Yamale](https://github.com/nholuongut/yamale) and [yamllint](https://github.com/nholuongut/yamllint) which require Python.

```yaml
name: Lint and Test Charts

on: pull_request

jobs:
  lint-test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Set up Helm
        uses: azure/setup-helm@v4.2.0
        with:
          version: v3.14.4

      - uses: actions/setup-python@v5
        with:
          python-version: '3.x'
          check-latest: true

      - name: Set up chart-testing
        uses: nholuongut/chart-testing-action@v2.7.0

      - name: Run chart-testing (list-changed)
        id: list-changed
        run: |
          changed=$(ct list-changed --target-branch ${{ github.event.repository.default_branch }})
          if [[ -n "$changed" ]]; then
            echo "changed=true" >> "$GITHUB_OUTPUT"
          fi

      - name: Run chart-testing (lint)
        if: steps.list-changed.outputs.changed == 'true'
        run: ct lint --target-branch ${{ github.event.repository.default_branch }}

      - name: Create kind cluster
        if: steps.list-changed.outputs.changed == 'true'
        uses: helm/kind-action@v1.10.0

      - name: Run chart-testing (install)
        if: steps.list-changed.outputs.changed == 'true'
        run: ct install --target-branch ${{ github.event.repository.default_branch }}
```

This uses [`nholuongut/kind-action`](https://www.github.com/nholuongut/kind-action) GitHub Action to spin up a [kind](https://kind.sigs.k8s.io/) Kubernetes cluster,
and [`nholuongut/chart-testing`](https://www.github.com/nholuongut/chart-testing) to lint and test your charts on every pull request.

## Upgrading from v1.x.x

v2.0.0 is a major release with breaking changes.
The action no longer wraps the chart-testing tool but simply installs it.
It is no longer run in a Docker container.
All `ct` options are now directly available without the additional abstraction layer.

## Code of conduct

Participation in the Helm community is governed by the [Code of Conduct](CODE_OF_CONDUCT.md).

# 🚀 I'm are always open to your feedback.  Please contact as bellow information:
### [Contact ]
* [Name: Nho Luong]
* [Skype](luongutnho_skype)
* [Github](https://github.com/nholuongut/)
* [Linkedin](https://www.linkedin.com/in/nholuong/)
* [Email Address](luongutnho@hotmail.com)
* [PayPal.me](https://www.paypal.com/paypalme/nholuongut)

![](https://i.imgur.com/waxVImv.png)
![](Donate.jpg)
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/nholuong)

# License
* Nho Luong (c). All Rights Reserved.🌟
