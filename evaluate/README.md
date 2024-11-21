<!--

     Copyright (c) 2023-present Sonatype, Inc. All rights reserved.
     Includes the third-party code listed at https://links.sonatype.com/products/clm/attributions.
     "Sonatype" is a trademark of Sonatype, Inc.

-->

# Sonatype Evaluate Action

A [GitHub Action](https://github.com/features/actions) for evaluations against a Sonatype Lifecycle instance in GitHub
workflows. It sets up [Sonatype CLI](https://help.sonatype.com/en/sonatype-iq-cli.html) and runs it to execute the
evaluation.

This is a convenience composite action that internally uses the following actions with some predefined parameters,
enabling developers to quickly start running Lifecycle evaluations:

- [Setup Java](https://github.com/actions/setup-java/blob/main/README.md) - makes Java available for GitHub runners by
  selecting an appropriate version for the Sonatype CLI;
- [Setup Sonatype CLI](../setup-iq-cli/README.md) - makes the latest version of Sonatype CLI available for GitHub
  runners;
- [Run Sonatype CLI](../run-iq-cli/README.md) - runs an evaluation using the tools set up in the previous two actions.

## Usage Example

```yaml
name: Sonatype Workflow
on: push
jobs:
  sonatype-cli:
    runs-on: ubuntu-latest
    steps:
      # some steps are omitted...
      # Check out your code
      - name: Checkout
        id: checkout
        uses: actions/checkout@v4
      # Perform an evaluation
      - name: Run evaluate  action
        id: evaluate
        uses: sonatype/actions/evaluate@v1
        with:
          iq-server-url: https://your.lifecycle.server
          username: ${{ secrets.LIFECYCLE_USERNAME }}
          password: ${{ secrets.LIFECYCLE_PASSWORD }}
          application-id: lifecycle-app
          scan-targets: package.json
      # Print out the results
      - name: Log evaluate action output
        run: echo "${{ steps.evaluate.outputs.scan-id }} ${{ steps.evaluate.outputs.report-url }}"
```

## Parameters

> **iq-server-url**\
> Lifecycle (IQ Server) URL. `Required`

> **username**\
> The username to authenticate with Lifecycle (IQ Server). `Required`

> **password**\
> The password to authenticate with Lifecycle (IQ Server). `Required`

> **application-id**\
> Lifecycle (IQ Server) application ID. When
> [Automatic Applications](https://help.sonatype.com/en/automatic-application.html) is enabled and the application ID
> has not yet been used, a new application will be created with the given ID. `Required`

> **organization-id**\
> The ID for the Lifecycle organization to which the application belongs. When automatic application creation is enabled
> and the application does not exist, it will be created under the organization having the provided organization ID. See
> [Automatic application creation with the CLI](https://help.sonatype.com/en/automatic-application-with-the-cli.html)
> for more details. `Optional`

> **scan-targets**\
> Space-separated list of paths to specific files, directories or docker images.
> [Apache Ant-styled patterns](https://ant.apache.org/manual/dirtasks.html#patterns) are allowed. See supported file
> formats in [Analysis](https://help.sonatype.com/en/analysis.html). `Required`

> **stage**\
> Specify the development lifecycle stage for the analysis. Defaults to the `build` stage. `Optional`

> **result-file**\
> If set, the analysis output (in JSON format) will be stored as a run artifact with the specified name. `Optional`

> **module-exclude**\
> Space-separated list of module files to ignore via
> [Apache Ant-styled patterns](https://ant.apache.org/manual/dirtasks.html#patterns). `Optional`

> **fail-on-policy-warnings**\
> Causes a failure of the evaluation if any warnings are encountered. Default: `false`. `Optional`

> **ignore-system-errors**\
> Ignore system errors (e.g. IO, Network, server, etc.) to avoid unintentional build failures. Default: `false`.
> `Optional`

> **ignore-scanning-errors**\
> Ignore scanning errors (e.g. invalid files, inaccessible files, etc). It is useful when the code base contains invalid
> files for testing purposes. Scanning these files may cause unintentional build failures. Default: `false`. `Optional`

> **debug**\
> Enables debug logging for troubleshooting. Use with caution as this log may expose sensitive information. Default:
> `false`. `Optional`

> **keep-scan-file**\
> Retains the scan file as an action artifact. Default: `false`. `Optional`

> **proxy**\
> Specify a proxy to use when connecting to Lifecycle (IQ Server). This property is set using the format `host[:port]`,
> otherwise, the CLI uses the default HTTP proxy set with the JVM.' `Optional`

> **proxy-user**\
> Specify proxy credentials in the following format: `username:password`. `Optional`

> **include-sha-256**\
> If set, SHA256 checksums are included in the scan file. Default: `false`. `Optional`

> **exclude-maven-dependency-management**\
> Enable this parameter to limit analysis to the projects dependencies section of a pom file while excluding the
> components under the dependencyManagement section. Default: `false`. `Optional`

> **sarif-file**\
> Generates a SARIF file with a specified name containing all identified vulnerabilities. The SARIF file extension must
> be .sarif or .json. The generated file will be uploaded as an action artifact. `Optional`

> **upload-sarif-file**\
> Enable this parameter to generate or use the generated sarif-file to update the "Code scanning" section under the
> GitHub repository's Security tab. Default: `false`. `Optional`\
> If sarif-file is not defined, the generated file will be named `result.sarif` by default, and it will be uploaded as
> an action artifact.\
> [GitHub Advanced Security](https://docs.github.com/en/get-started/learning-about-github/about-github-advanced-security) is
> available for GitHub Enterprise and public repositories.

## Output

> **scan-id**\
> Scan id result of the run operation. It can be used to access the Lifecycle report of relater artifacts.

> **report-url**\
> Link to navigate directly to the analysis report in Lifecycle (IQ Server).

> **sarif-file**\
> Name of the generated SARIF file containing all found vulnerabilities, if configured.
