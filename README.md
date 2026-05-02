
# DevOps Workflows

Reusable GitHub Actions workflows for .NET projects.

This repository centralizes shared CI/CD logic so it can be reused across multiple repositories using **GitHub reusable workflows (`workflow_call`)**. It helps standardize build, test, coverage, and packaging processes across your .NET ecosystem.

---

## 🚀 Available Workflows

### `build-dotnet`

A complete CI workflow for .NET projects that includes:

* ✅ Restore & build
* ✅ Unit test execution
* ✅ Test reporting (TRX)
* ✅ Code coverage (Coverlet + ReportGenerator)
* ✅ Coverage merge & publishing (Codecov)
* ✅ Optional NuGet packaging

---

## 📦 Features

### Build Job

* Restores dependencies
* Builds in `Release` mode
* Runs all tests
* Publishes test results using `dorny/test-reporter`

### Coverage Jobs

* Runs per test project (matrix strategy)
* Collects coverage using `XPlat Code Coverage`
* Normalizes coverage paths
* Uploads coverage artifacts

### Merge Coverage

* Merges all coverage reports
* Generates:

  * HTML report
  * Cobertura report
* Uploads artifacts
* Sends coverage to Codecov

### Pack Job (Optional)

* Builds and packs NuGet packages
* Searches specified folders for `.nupkg` files
* Publishes all `.nupkg` artifacts from configured locations

---

## 🧩 Usage

To use this shared workflow in another repository, create a workflow like this:

```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:

permissions:
  contents: read
  checks: write
  packages: write
  pull-requests: write

jobs:
  build:
    uses: xaviersolau/devops-workflows/.github/workflows/build-dotnet.yml@main
    with:
      test-projects: '["tests/MyProject.Tests", "tests/MyOther.Tests"]'
      run-pack: true
      pack-folders: '["src/libs", "src/packages"]'
```

---

## ⚙️ Inputs

| Name                        | Required | Default                | Description                      |
| --------------------------- | -------- | ---------------------- | -------------------------------- |
| `dotnet-version`            | ❌        | `10.0.x`               | .NET SDK version                 |
| `test-projects`             | ✅        | —                      | JSON array of test project paths |
| `coverlet-runsettings-file` | ❌        | `coverlet.runsettings` | Coverage configuration file      |
| `run-pack`                  | ❌        | `false`                | Enable NuGet packaging           |
| `pack-folders`              | ❌        | `["src/libs"]`        | JSON array of folders to search for .nupkg files |

---

## 📊 Example: Multiple Test Projects

```yaml
with:
  test-projects: >
  [
    "tests/ProjectA.Tests",
    "tests/ProjectB.Tests"
  ]
```

---

## 📁 Artifacts Produced

* `coverage-html` → HTML coverage report
* `coverage-*` → Individual coverage files per test project
* `NugetPackages` → Generated `.nupkg` files from configured folders (if enabled)

---

## 📦 NuGet Packaging Configuration

The `pack` job collects `.nupkg` files from one or more folders using the `pack-folders` input.

**Single folder (default):**
```yaml
with:
  run-pack: true
  # Uses default: ["src/libs"]
```

**Custom single folder:**
```yaml
with:
  run-pack: true
  pack-folders: '["src/packages"]'
```

**Multiple folders:**
```yaml
with:
  run-pack: true
  pack-folders: '["src/libs", "src/packages", "build/output"]'
```

All `.nupkg` files from all specified folders will be collected into a single `NugetPackages` artifact.

---

## 🔐 Required Secrets

If using Codecov upload:

* `CODECOV_TOKEN`

---

## 🛠️ Requirements

* Test projects must support:

  * `dotnet test`
  * TRX logging
  * Coverlet data collection

* A `coverlet.runsettings` file

---

## 🧠 Design Principles

* **Reusable**: One workflow, many repositories
* **Modular**: Jobs split by responsibility
* **Scalable**: Matrix-based test execution
* **Fast**: NuGet caching enabled
* **Standardized**: Consistent CI behavior across projects

---

## 🔄 Versioning Strategy

Reference a specific version (recommended):

```yaml
uses: xaviersolau/devops-workflows/.github/workflows/build-dotnet.yml@v1.1.0
```

Or track main (less stable):

```yaml
uses: xaviersolau/devops-workflows/.github/workflows/build-dotnet.yml@main
```

---

## 🤝 Contributing

Contributions are welcome! Feel free to:

* Open issues
* Suggest improvements
* Submit pull requests

---

## 📄 License

MIT License

---
