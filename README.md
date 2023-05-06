# Run Helm chart unit tests

Composite action that installs the desired version of Helm through
[azure/setup-helm][setup-helm], the latest version of the
[helm-unittest][helm-unittest] plugin, and then executes chart unit tests for
the specified charts or, if none are specified, for every chart found in the
repository.

[setup-helm]: https://github.com/azure/setup-helm
[helm-unittest]: https://github.com/helm-unittest/helm-unittest

## Options

| Input          | Description                                                                                   | Required | Default   |
|:---------------|:----------------------------------------------------------------------------------------------|:---------|:----------|
| `flags`        | Which flags to pass to helm-unittest when running unit tests.                                 | No       | `--color` |
| `charts`       | Paths to the charts to be tested, separated by spaces. If empty, all charts found are tested. | No       | `""`      |
| `install-mode` | One of `"force"`, `"if-not-present"`, or `""`. More information below.                        | No       | `""`      |
| `helm-version` | Which version of Helm to install. Passed to [azure/setup-helm][setup-helm].                   | Yes      | `latest`  |
| `github-token` | GitHub token for the workflow. Passed to [azure/setup-helm][setup-helm]. Not always needed!   | No       | `""`      |

The `github-token` input is necessary only when installing the latest version
of Helm, to overcome GitHub API rate limits. If not given, refer to the
[azure/setup-helm][setup-helm] README for more information.

### Installation mode

The `install-mode` input controls how `helm-unittest` should be installed.
These are the available installation modes:

| install-mode   | Behavior                                                                    |
|:---------------|:----------------------------------------------------------------------------|
| force          | Installs over any previous version of the plugin.                           |
| if-not-present | Skips installation if there's a plugin called `unittest` already installed. |
| _empty_        | Attempts to install the plugin normally. Will fail if already installed.    |

## Examples

This composite action can be used in any workflow that requires Helm unit
tests. For every chart it finds, it will run `helm dependency update` to ensure
chart dependencies are present.

```yaml
name: CI

on: pull_request

jobs:
  unittest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: d3adb5/helm-unittest-action@v2
        with:
          helm-version: v3.8.0
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

Note that after the action itself, Helm is installed, so you can run additional
Helm commands in the same action if you so choose:

```yaml
name: CI

on: pull_request

jobs:
  unittest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: d3adb5/helm-unittest-action@v2
      - run: helm lint my-chart
```
