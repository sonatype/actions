<!--

     Copyright (c) 2023-present Sonatype, Inc. All rights reserved.
     Includes the third-party code listed at https://links.sonatype.com/products/clm/attributions.
     "Sonatype" is a trademark of Sonatype, Inc.

-->

# Sonatype CLI Run Action

A [GitHub Action](https://github.com/features/actions) for running
[Sonatype CLI](https://help.sonatype.com/en/sonatype-iq-cli.html) in GitHub workflows.

### Java Requirements

This action (i.e. `sonatype/actions/run-iq-cli`) needs to have Sonatype CLI and a JDK properly configured to be able to
work. [Setup Sonatype CLI](../setup-iq-cli/README.md) action provided in this action set.

Sonatype CLI versions 174 and older require Java 8 or 11 to run. For Sonatype CLI release 175 and above, we recommend
using Java 17, which will be the minimum supported Java version for all the releases beyond 179.

## Usage Example

```yaml
name: Sonatype Workflow
on: push
jobs:
  sonatype-cli:
    runs-on: ubuntu-latest
    steps:
      # some steps are omitted...
      # Make Sonatype CLI available to GitHub runners
      - name: Setup Sonatype CLI
        uses: sonatype/actions/setup-iq-cli@v1
        with:
          iq-cli-version: 1.179.0-01

      # Sonatype CLI requires Java to run
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'

      # Run Sonatype CLI
      - name: Run Sonatype CLI
        uses: sonatype/actions/run-iq-cli@v1
        with:
          iq-server-url: https://your.lifecycle.server
          username: ${{ secrets.LIFECYCLE_USERNAME }}
          password: ${{ secrets.LIFECYCLE_PASSWORD }}
          application-id: lifecycle-app
          scan-targets: target/*.jar
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
> Space separated list of paths to specific files, directories, or docker images.
> [Apache Ant-styled patterns](https://ant.apache.org/manual/dirtasks.html#patterns) are allowed. See supported file
> formats in [Analysis](https://help.sonatype.com/en/analysis.html). `Required`

> **iq-cli-version**\
> The CLI version to be used for evaluation, usually installed by a previous `setup-iq-cli` action. Defaults to the
> latest CLI found on the current GitHub runner. `Optional`

> **stage**\
> Specify the development lifecycle stage for the analysis. Defaults to the `build` stage. `Optional`

> **result-file**\
> If set, the analysis output (in JSON format) will be uploaded as a run artifact with the specified name. `Optional`

> **module-exclude**\
> Space separated list of module files to ignore via Apache Ant-styled patterns. `Optional`

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
> Retains and uploads the scan file as an action artifact. Default: `false`. `Optional`

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

## Output

> **scan-id**\
> Scan id result of the run operation. It can be used to access the Lifecycle report of relater artifacts.

> **report-url**\
> Use this link to navigate directly to the analysis report in Lifecycle (IQ Server).
