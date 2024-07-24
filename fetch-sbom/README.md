<!--

     Copyright (c) 2023-present Sonatype, Inc. All rights reserved.
     Includes the third-party code listed at https://links.sonatype.com/products/clm/attributions.
     "Sonatype" is a trademark of Sonatype, Inc.

-->

# Fetch SBOM Action

A [GitHub Action](https://github.com/features/actions) for retrieving an SBOM (Software Bill of Materials) file
associated with a previous Lifecycle evaluation. It supports both the [CycloneDX](https://cyclonedx.org/) and
[SPDX](https://spdx.dev/) standards.

## Usage Example

```yaml
name: Sonatype Workflow
on: push
jobs:
  sonatype-cli:
    runs-on: ubuntu-latest
    steps:
      # some steps are omitted...
      # Evaluate Sonatype CLI
      - name: Evaluate Sonatype CLI
        id: evaluate
        uses: sonatype/actions/evaluate@v1
        with:
          iq-server-url: https://your.lifecycle.server
          username: ${{ secrets.LIFECYCLE_USERNAME }}
          password: ${{ secrets.LIFECYCLE_PASSWORD }}
          application-id: lifecycle-app
          scan-targets: target/*.jar
      # Fetch the SBOM file associated with the previous Sonatype CLI run
      - name: Fetch SBOM
        uses: sonatype/actions/fetch-sbom@v1
        if: ( success() || failure() ) && steps.evaluate.outputs.scan-id
        with:
          iq-server-url: https://your.lifecycle.server
          username: ${{ secrets.LIFECYCLE_USERNAME }}
          password: ${{ secrets.LIFECYCLE_PASSWORD }}
          application-id: lifecycle-app
          scan-id: ${{ steps.evaluate.outputs.scan-id }}
          sbom-standard: cyclonedx
          sbom-version: 1.5
          artifact-name: my-sbom
```

## Parameters

> **iq-server-url**\
> Lifecycle (IQ Server) URL. `Required`

> **username**\
> The username to authenticate with Lifecycle (IQ Server). `Required`

> **password**\
> The password to authenticate with Lifecycle (IQ Server). `Required`

> **application-id**\
> Lifecycle (IQ Server) application ID. `Required`

> **scan-id**\
> The SBOM file associated with the provided scan ID is fetched and stored as an action artifact. `Required`

> **sbom-standard**\
> The SBOM standard: `spdx` or `cyclonedx`. `Required`

> **sbom-version**\
> The version of the SBOM standard.\
> Available CycloneDX versions: `1.2`, `1.3`, `1.4`, `1.5`. Available SPDX versions: `2.3`. `Required`

> **sbom-format**\
> The output file format: `json` or `xml`. Default: `json`. `Optional`

> **artifact-name**\
> The name of the artifact to be stored. Default: `sbom-document`. `Optional`

> **push-dependency-graph**\
> If set, the dependency information from the fetched SBOM will be available in GitHub Insights Dependency Graph.
> Default: `false`. `Optional`

> **token**\
> GitHub Personal Access Token (PAT). Defaults to the PAT provided by the actions runner. `Optional`
