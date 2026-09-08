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
        uses: actions/setup-java@v5
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

## .NET Reachability Usage Example

### Prerequisites

- .NET 8 SDK or runtime installed on the runner

```yaml
- name: Run Sonatype CLI with .NET Reachability
  uses: sonatype/actions/run-iq-cli@v1
  with:
    iq-server-url: https://your.lifecycle.server
    username: ${{ secrets.LIFECYCLE_USERNAME }}
    password: ${{ secrets.LIFECYCLE_PASSWORD }}
    application-id: my-dotnet-app
    scan-targets: ./publish
    enable-reachability-dotnet: true
    reachability-dotnet-namespaces: MyCompany.App MyCompany.Core
    reachability-dotnet-entrypoint-strategy: DOTNET_MAIN
```

### Choosing an Entrypoint Strategy

| Strategy              | Use Case                                                    |
| --------------------- | ----------------------------------------------------------- |
| `DOTNET_MAIN`         | Console applications with a `Main` method entry point       |
| `CONCRETE`            | ASP.NET applications or apps with well-defined entry points |
| `PUBLIC_CONCRETE`     | Class libraries where public methods are the entry points   |
| `ACCESSIBLE_CONCRETE` | Libraries with broader access patterns                      |
| `ALL`                 | Comprehensive analysis covering all possible entry points   |

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
> Space-separated list of paths to specific files, directories, or docker images.
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
> Retains and uploads the scan file as an action artifact. Default: `false`. `Optional`

> **proxy**\
> Specify a proxy to use when connecting to Lifecycle (IQ Server). This property is set using the format `host[:port]`,
> otherwise, the CLI uses the default HTTP proxy set with the JVM. Do not include a scheme: a value such as
> `http://proxy:8888` is parsed as the host name `http`. `Optional`

> **proxy-user**\
> Specify proxy credentials in the following format: `username:password`. The password cannot contain a colon.
> `Optional`

> **include-sha-256**\
> If set, SHA256 checksums are included in the scan file. Default: `false`. `Optional`

> **exclude-maven-dependency-management**\
> Enable this parameter to limit analysis to the projects dependencies section of a pom file while excluding the
> components under the dependencyManagement section. Default: `false`. `Optional`

> **sarif-file**\
> Generates a SARIF file with a specified name containing all identified vulnerabilities. The SARIF file extension must
> be .sarif or .json. The generated file will be uploaded as an action artifact. `Optional`

> **enable-reachability**\
> Since version 1.3.0, you can perform a reachability analysis in Java or JVM language binaries to determine the method
> signatures that trigger a security vulnerability. Default: `false`. `Optional`

> **reachability-namespaces**\
> Since version 1.3.0, you can limit the reachability analysis to a specific namespace for faster, more precise results.
> Multiple namespaces can be delimited by space. Default: `empty`. `Optional`

> **reachability-entrypoint-strategy**\
> Entrypoint strategy for Java reachability analysis. `Optional`

> **enable-reachability-js**\
> Enable JavaScript reachability analysis. Default: `false`. `Optional`

> **reachability-js-sources**\
> JavaScript source patterns for reachability analysis (required for JS reachability; these are app files; do not
> include tests or node_modules files here). Default: `empty`. `Optional`

> **reachability-js-excludes**\
> JavaScript exclude patterns for reachability analysis (e.g. tests and other source files that are not relevant for the
> analysis). Default: `empty`. `Optional`

> **reachability-node-path**\
> Path to Node.js executable. `Optional`

> **reachability-js-project-root**\
> JavaScript project root directory (i.e. where the main package.json file resides). `Optional`

> **enable-reachability-dotnet**\
> Enable .NET reachability analysis. Requires .NET 8 SDK or runtime to be available on the runner. Default: `false`.
> `Optional`

> **reachability-dotnet-namespaces**\
> Namespace prefixes to scope entry points for .NET reachability analysis. Multiple namespaces can be delimited by
> space. Default: `empty`. `Optional`

> **reachability-dotnet-entrypoint-strategy**\
> Entrypoint strategy for .NET reachability analysis. `Optional`

> **reachability-dotnet-path**\
> Absolute path to the dotnet executable. When not specified, assumes `dotnet` is available on the system PATH. This
> value maps to bomxray's `dotnet-path` configuration property. Use this when the .NET SDK is installed in a
> non-standard location. `Optional`

> **ignore-reachability-errors**\
> Ignore reachability analysis errors (reachability analysis failed to run). Default: `false`. `Optional`

## Output

> **scan-id**\
> Scan id result of the run operation. It can be used to access the Lifecycle report of relater artifacts.

> **report-url**\
> Link to navigate directly to the analysis report in Lifecycle (IQ Server).

> **priorities-url**\
> Link to navigate directly to the priorities report in Lifecycle (IQ Server).

> **sarif-file**\
> Name of the generated SARIF file containing all found vulnerabilities, if configured.

## Troubleshooting .NET Reachability

### .NET 8 runtime not found on PATH

**Symptom:** The analysis fails with an error indicating that the `dotnet` command cannot be found.

**Solution:** Either install .NET 8 SDK using the `actions/setup-dotnet@v4` action (recommended), or provide the
absolute path to the `dotnet` executable via the `reachability-dotnet-path` input:

```yaml
- name: Run Sonatype CLI
  uses: sonatype/actions/run-iq-cli@v1
  with:
    # ...other inputs...
    enable-reachability-dotnet: true
    reachability-dotnet-path: /usr/share/dotnet/dotnet
```

### No .NET assemblies found

**Symptom:** The analysis completes but reports no reachability data, or warns that no assemblies were found.

**Solution:** Ensure your .NET project is built or published before running the scan. The reachability analysis operates
on compiled assemblies (`.dll` files), not source code. Add a build step before the scan:

```yaml
- name: Build .NET project
  run: dotnet publish -c Release -o ./publish
```

### Parser timeout for large solutions

**Symptom:** The analysis times out or takes an excessively long time for large .NET solutions.

**Solution:** Narrow the scope of the analysis using `reachability-dotnet-namespaces` to limit analysis to your
application's namespaces, and choose an appropriate entrypoint strategy to reduce the number of entry points analyzed:

```yaml
- name: Run Sonatype CLI
  uses: sonatype/actions/run-iq-cli@v1
  with:
    # ...other inputs...
    enable-reachability-dotnet: true
    reachability-dotnet-namespaces: MyCompany.MyApp
    reachability-dotnet-entrypoint-strategy: DOTNET_MAIN
```
