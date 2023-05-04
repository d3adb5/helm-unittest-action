# Helm Unit Tests Action

Composite action that installs the latest version of Helm through [helm's
install script][helm-install], the latest version of the
[helm-unittest][helm-unittest] plugin, and then executes chart unit tests for
the specified charts or, if none are specified, for every chart found in the
repository.

[helm-install]: https://github.com/helm/helm/blob/main/scripts/get-helm-3
[helm-unittest]: https://github.com/helm-unittest/helm-unittest

## Options

| Input        | Description                                                                                   | Required | Default   |
|:-------------|:----------------------------------------------------------------------------------------------|:---------|:----------|
| flags        | Which flags to pass to helm-unittest when running unit tests.                                 | No       | `--color` |
| charts       | Paths to the charts to be tested, separated by spaces. If empty, all charts found are tested. | No       | `""`      |
| install-mode | One of `"force"`, `"if-not-present"`, or `""`. More information below.                        | No       | `""`      |

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
