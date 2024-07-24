<!--

     Copyright (c) 2023-present Sonatype, Inc. All rights reserved.
     Includes the third-party code listed at https://links.sonatype.com/products/clm/attributions.
     "Sonatype" is a trademark of Sonatype, Inc.

-->

# Sonatype GitHub Actions

A set of [GitHub Actions](https://github.com/features/actions) for interacting with different [Sonatype](https://www.sonatype.com/) products.

The action set currently contains a couple of top-level actions:

- [Evaluate Action](evaluate/README.md)
- [Fetch SBOM](fetch-sbom/README.md)

Additionally, for developers who require more fine-grained control over the actions' configuration, a set of advanced actions are also provided:

- [Setup Sonatype CLI](setup-iq-cli/README.md)
- [Run Sonatype CLI](run-iq-cli/README.md)

## Usage

Here's a typical usage example that evaluates an npm project against a Sonatype Lifecycle instance and retrieves the associated SBOM (Software Bill of Materials) file:

```yaml
name: Sonatype Workflow
on: push
jobs:
  sonatype-cli:
    runs-on: ubuntu-latest
    steps:
      # Check out your code
      - name: Checkout
        id: checkout
        uses: actions/checkout@v4
      # Perform an evaluation 
      - name: Run evaluate action
        id: evaluate
        uses: sonatype/actions/evaluate@v1
        with:
          iq-server-url: https://your.lifecycle.server
          username: ${{ secrets.LIFECYCLE_USERNAME }}
          password: ${{ secrets.LIFECYCLE_PASSWORD }}
          application-id: lifecycle-app
          scan-targets: package.json package-lock.json
      # Fetch the SBOM file associated with the evaluation
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
          sbom-format: json
          artifact-name: lifecycle-app-sbom.json
```

For more details on the supported parameters for each action, refer to the individual action documentation.
