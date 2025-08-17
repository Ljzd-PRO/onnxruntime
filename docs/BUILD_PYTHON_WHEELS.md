# Multi-Architecture Python Wheel Build Workflow

This document describes the new GitHub Actions workflow for building Python wheel packages for different architectures.

## Overview

The `build-python-wheels-multi-arch.yml` workflow enables building ONNX Runtime Python wheels for multiple architectures including:
- **armv7** (32-bit ARM) - Primary focus
- **aarch64** (64-bit ARM) 
- **x86_64** (64-bit x86)

## Usage

### Manual Trigger

The workflow can be triggered manually via GitHub Actions UI:

1. Go to **Actions** tab in the GitHub repository
2. Select **Build Python Wheels - Multi Architecture** workflow
3. Click **Run workflow**
4. Select the desired parameters:
   - **Architecture**: `armv7`, `aarch64`, or `x86_64`
   - **Build Configuration**: `Release` or `Debug`  
   - **Python Version**: `3.8`, `3.9`, `3.10`, `3.11`, or `3.12`

### Parameters

| Parameter | Description | Default | Options |
|-----------|-------------|---------|---------|
| `architecture` | Target architecture for the build | `armv7` | `armv7`, `aarch64`, `x86_64` |
| `build_config` | Build configuration | `Release` | `Release`, `Debug` |
| `python_version` | Python version to use | `3.10` | `3.8`, `3.9`, `3.10`, `3.11`, `3.12` |

## Cross-Compilation Support

### ARMv7 (32-bit ARM)

For ARMv7 builds, the workflow:
- Installs ARM cross-compilation tools (`gcc-arm-linux-gnueabihf`)
- Uses the updated `cmake/linux_arm32_crosscompile_toolchain.cmake` toolchain file
- Sets `AUDITWHEEL_PLAT=manylinux2014_armv7l` for proper wheel tagging
- Configures compiler flags for ARMv7 with NEON support

### AArch64 and x86_64

For AArch64 and x86_64 builds, the workflow uses native compilation on the respective runners.

## Output Artifacts

The workflow produces:
- Python wheel files (`.whl`) compatible with the target architecture
- Artifacts are uploaded with descriptive names: `onnxruntime-wheel-{architecture}-{config}-py{version}`
- Artifacts are retained for 30 days

## Build Summary

Each workflow run provides a summary including:
- Target architecture and build configuration
- Python version used
- List of generated wheel files

## Toolchain Files

The workflow uses the following toolchain files:
- `cmake/linux_arm32_crosscompile_toolchain.cmake` - For ARMv7 cross-compilation

## Dependencies

The workflow automatically installs:
- System dependencies (build tools, cmake, etc.)
- Python dependencies from `requirements.txt`
- Architecture-specific cross-compilation tools (for ARMv7)

## Notes

- The workflow currently focuses on ARMv7 support as requested
- Additional architectures can be easily added by extending the choice options
- Cross-compilation is used for ARM32/armv7 builds on x86_64 runners
- Tests are skipped during wheel builds to speed up the process
- The workflow uses GitHub's 1ES.Pool runners for consistency with existing workflows