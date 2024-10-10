<!--

     Copyright (c) 2023-present Sonatype, Inc. All rights reserved.
     Includes the third-party code listed at https://links.sonatype.com/products/clm/attributions.
     "Sonatype" is a trademark of Sonatype, Inc.

-->

# Sonatype CLI Setup Action

A [GitHub Action](https://github.com/features/actions) for setting up
[Sonatype CLI](https://help.sonatype.com/en/sonatype-iq-cli.html) as a tool that is available for GitHub runners and
subsequent actions. The action downloads and configures the requested version of the Sonatype CLI.

## Usage Example

Here's an example of how the setup action can be used in your code. Note that this is a prerequisite for the
[Run Sonatype CLI](../run-iq-cli/README.md) action provided in this action set.

```yaml
name: Sonatype Workflow
on: push
jobs:
  sonatype-cli:
    runs-on: ubuntu-latest
    steps:
      - name: Setup Sonatype CLI
        uses: sonatype/actions/setup-iq-cli@v1
        with:
          iq-cli-version: 1.179.0-01
```

Alternatively, instead of providing `iq-cli-version`, you can provide the `iq-cli-download-url` parameter. In this case,
the Sonatype CLI JAR file will be downloaded from the specified location URL.

## Parameters

> **iq-cli-version**\
> Sonatype CLI version to be used. It could be a full version (e.g. 1.179.0-01), a short version (e.g. 179) or `latest`.
> This parameter must be provided if `iq-cli-download-url` is not specified.

> **iq-cli-download-url**\
> The URL from where the IQ CLI jar file will be downloaded. This parameter must be provided if `iq-cli-version` is not
> specified.

## Output

> **iq-cli-version**\
> Semver valid version of the IQ CLI configured. It can be used as an input parameter for run-iq-cli.
