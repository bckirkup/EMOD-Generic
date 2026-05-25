---
name: build-and-run-emod
description: Build and run the EMOD-Generic epidemiological modeling software. Covers SCons build, running simulations, regression tests, and schema generation.
---

# Build and Run EMOD-Generic

## Prerequisites

- Linux (Ubuntu 20.04+ recommended)
- SCons build system (`pip install scons`)
- C++ compiler (g++ with C++17 support)
- Docker (optional, for using the official build environment)
- Python 3.x (for regression tests and embedded Python scripts)

## Devin Secrets Needed

None — EMOD builds and runs with bundled source and test data.

## Build

### Option 1: Docker build environment (recommended)
```bash
docker pull ghcr.io/emod-hub/emod-ubuntu-buildenv:latest
docker run --rm -v $(pwd):/src -w /src ghcr.io/emod-hub/emod-ubuntu-buildenv:latest \
  scons --Release --jobs=4 --Disease=Generic
```

### Option 2: Native build
```bash
# Install SCons
pip install scons

# Build the Generic disease variant
scons --Release --jobs=4 --Disease=Generic
```

The compiled binary will be at: `build/x64/Release/Eradication/Eradication`

### Build options
```bash
# Build all disease types
scons --Release --jobs=4

# Build with TestSugar (for science tests)
scons --Release --jobs=4 --Disease=Generic --TestSugar
```

### Strip the binary (production)
```bash
strip ./build/x64/Release/Eradication/Eradication
```

## Generate Schema

```bash
./build/x64/Release/Eradication/Eradication --get-schema --schema-path schema.json
```

## Running Simulations

### Run a regression test
```bash
cd Regression/Generic
# Each subdirectory contains a simulation scenario with config and campaign files
ls inputs/
```

### Run with the Eradication binary
```bash
./build/x64/Release/Eradication/Eradication --config config.json --input-path ./inputs --output-path ./output
```

## Testing

### Component tests
```bash
cd componentTests
# Run with test data from componentTests/testdata/
```

### Regression tests
Regression tests are organized by disease type in `Regression/`:

| Directory | Disease Type |
|-----------|-------------|
| `Regression/Generic/` | Generic disease simulations |
| `Regression/Dengue/` | Dengue-specific scenarios |
| `Regression/HIV/` | HIV-specific scenarios |
| `Regression/Malaria/` | Malaria-specific scenarios |
| `Regression/Polio/` | Polio-specific scenarios |
| `Regression/Typhoid/` | Typhoid-specific scenarios |
| `Regression/Vector/` | Vector-borne disease scenarios |

### Science tests
```bash
# Build with TestSugar flag
scons --Release --jobs=4 --Disease=Generic --TestSugar

# Run science tests from Regression/*_Science/ directories
```

## Key Source Directories

| Directory | Contents |
|-----------|----------|
| `Eradication/` | Core simulation engine source |
| `interventions/` | Intervention implementation code |
| `campaign/` | Campaign event processing |
| `reporters/` | Output report generators |
| `utils/` | Utility libraries |
| `Scripts/` | Build and utility scripts |
| `docs/` | Documentation (Markdown, CSV, JSON) |

## CI Workflows

| Workflow | Purpose |
|----------|---------|
| `build_only.yml` | Build Release or TestSugar |
| `build_publish.yml` | Build and publish artifacts |
| `test_regression_component.yml` | Run regression and component tests |
| `test_science.yml` | Run science validation tests |
| `mkdocs_build.yml` | Build documentation |
| `mkdocs_deploy.yml` | Deploy documentation |

## Notes

- EMOD is no longer actively maintained by IDM/Gates Foundation — community contributions welcome
- Uses Docker build environment `ghcr.io/emod-hub/emod-ubuntu-buildenv` for reproducible builds
- The `--Disease` flag controls which disease modules are compiled
- Generic disease branch is suitable for measles, rubella, polio, etc.
