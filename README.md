# Helm Unit Tests Action

Composite action that installs the latest version of Helm 3 through [helm's
install script][helm-install], the [helm-unittest][helm-unittest] plugin, and
then executes chart unit tests for the specified charts or for every chart in
the repository.

[helm-install]: https://github.com/helm/helm/blob/main/scripts/get-helm-3
[helm-unittest]: https://github.com/helm-unittest/helm-unittest

## Options

There are two optional inputs for this action:

### flags

What flags to pass to the `helm unittest` command. By default, we expect colors
in our output by passing `--color`. Helm v2 is no longer supported as of
helm-unittest version v0.3.0, so we're no longer passing `-3`.

- Default: `"--color"`
- Required: no

### charts

Paths to the charts that will be tested by the action, separated by spaces. If
empty, every directory containing a `Chart.yaml` file is assumed to be a chart
and will be tested.

- Default: `""`
- Required: no

## Examples

The following example is of a CI workflow for a repository containing Helm
charts. It will run `helm dependency update` on each chart and subsequently
`helm unittest -3 --color` for all of them at once.

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
Helm commands in the same action:

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
