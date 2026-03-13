# azure-pipeline-templates

Reusable Azure DevOps Pipeline YAML templates for direct referencing from pipeline files across Azure projects.

## Usage

Reference this repository as a resource in your pipeline, then include any template using the `template` keyword.

```yaml
resources:
  repositories:
    - repository: templates
      type: github
      name: showcase-games-com/azure-pipeline-templates
      ref: refs/heads/main
      endpoint: <your-github-service-connection>
```

---

## Available Templates

### Step Templates

#### `templates/steps/dotnet-build.yml`

Restores NuGet packages and builds a .NET project.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `buildConfiguration` | string | `Release` | Build configuration (e.g. `Debug`, `Release`) |
| `projectPath` | string | `**/*.csproj` | Glob pattern for project files to build |
| `dotnetVersion` | string | `8.x` | .NET SDK version to install |

**Example**

```yaml
steps:
  - template: templates/steps/dotnet-build.yml@templates
    parameters:
      buildConfiguration: Release
      projectPath: src/MyApp/*.csproj
      dotnetVersion: '8.x'
```

---

#### `templates/steps/dotnet-test.yml`

Runs .NET tests and optionally publishes test results and code coverage.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `buildConfiguration` | string | `Release` | Build configuration |
| `projectPath` | string | `**/*Tests.csproj` | Glob pattern for test project files |
| `publishTestResults` | boolean | `true` | Publish test results to the pipeline |
| `collectCoverage` | boolean | `true` | Collect and publish code coverage (Cobertura) |

**Example**

```yaml
steps:
  - template: templates/steps/dotnet-test.yml@templates
    parameters:
      projectPath: tests/**/*Tests.csproj
      collectCoverage: true
```

---

### Job Templates

#### `templates/jobs/dotnet-build-test.yml`

A single job that builds and tests a .NET solution.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `buildConfiguration` | string | `Release` | Build configuration |
| `projectPath` | string | `**/*.csproj` | Glob pattern for project files |
| `testProjectPath` | string | `**/*Tests.csproj` | Glob pattern for test project files |
| `dotnetVersion` | string | `8.x` | .NET SDK version to install |
| `publishTestResults` | boolean | `true` | Publish test results |
| `collectCoverage` | boolean | `true` | Collect and publish code coverage |
| `pool` | object | `vmImage: ubuntu-latest` | Agent pool configuration |

**Example**

```yaml
jobs:
  - template: templates/jobs/dotnet-build-test.yml@templates
    parameters:
      projectPath: src/**/*.csproj
      testProjectPath: tests/**/*Tests.csproj
      pool:
        vmImage: windows-latest
```

---

### Stage Templates

#### `templates/stages/ci.yml`

A complete CI stage wrapping the build-and-test job.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `buildConfiguration` | string | `Release` | Build configuration |
| `projectPath` | string | `**/*.csproj` | Glob pattern for project files |
| `testProjectPath` | string | `**/*Tests.csproj` | Glob pattern for test project files |
| `dotnetVersion` | string | `8.x` | .NET SDK version to install |
| `publishTestResults` | boolean | `true` | Publish test results |
| `collectCoverage` | boolean | `true` | Collect and publish code coverage |
| `pool` | object | `vmImage: ubuntu-latest` | Agent pool configuration |

**Example**

```yaml
trigger:
  - main

resources:
  repositories:
    - repository: templates
      type: github
      name: showcase-games-com/azure-pipeline-templates
      ref: refs/heads/main
      endpoint: <your-github-service-connection>

stages:
  - template: templates/stages/ci.yml@templates
    parameters:
      projectPath: src/**/*.csproj
      testProjectPath: tests/**/*Tests.csproj
```

---

## Template Structure

```
templates/
├── steps/
│   ├── dotnet-build.yml        # Restore + build .NET project
│   └── dotnet-test.yml         # Run tests + publish results/coverage
├── jobs/
│   └── dotnet-build-test.yml   # Build-and-test job
└── stages/
    └── ci.yml                  # Full CI stage
```