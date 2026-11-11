# Timeline for Infection Clusters tool in R (TICLR) --- Portable Version

**Version:** 2.1.0.0

## Introduction

This is a portable version of the Timeline for Infection Clusters tool in R (TICLR). See github.com/ukhsa-collaboration/TICLR for the source repository.

This version is designed to be run as a standalone portable application. It comes pre-packaged with R and all the necessary packages required (no additional installation needed).

## Requirements

* Currently only supports Windows (tested on Windows 10 and 11)

## Usage

* Download the portable version `.zip` file.
* Extract to a local directory (avoid network drives or cloud-synced folders).
* Run `run_app.bat` to start the application (you may need to right click and select "Run as administrator").
* Follow the on-screen instructions to use the TICLR tool.

When ready to close the application, simply exit the TICLR batch window.

## Developer notes

`portR`[https://github.com/agerada/portR] is used to build the portable version of TICLR.

These are the build requirements:

1. R >= 4.2.0
2. `renv` package installed in R
3. Additional `portR` requirements as specified in the [portR repository](https://github.com/agerada/portR).

### System requirements for build host

The build host and target machine determine which tools are required.

| Host machine | Target machine | Requirements |
|---|---|---|
| macOS / Linux | Windows | innoextract (recommended) or 7z for extracting the R installer |
| macOS | macOS | pkgutil and hdiutil (included on macOS) |
| Any | Creating archives | zip command (standard on most systems) |

For Windows builds on macOS/Linux:

```bash
# macOS
brew install innoextract

# Ubuntu/Debian
sudo apt install innoextract
```

Follow these steps to build:

1. Clone this fork of the TICLR repository: 

```bash
git clone https://github.com/agerada/TICLR.git
```
2. Navigate into the cloned repository:

```bash
cd TICLR
```

3. Start R

```bash
R
```

4. Install `TICLR` to the local `renv` library:

```R
renv::install(".")
```

5. **Important:** Use `renv` snapshot to lock the package versions:

```r
renv::snapshot(type = "all")
```

6. Build the portable version using `portR`:

```r
library(portR)
build_windows(  project_path = ".", 
 r_version = "4.5.2", 
 shiny_command = "TICLR::run_app()",
 extra_dirs = c("inst"),
 create_zip = TRUE,
 zip_name = "TICLR.zip"
)
```

## Automated GitHub release (main branch)

This repository includes a GitHub Actions workflow at `.github/workflows/release-portable.yml`.

How it works:

1. Trigger: runs automatically on `push` to `main`.
2. Build: restores `renv`, installs `portR`, and builds a portable Windows bundle via `portR::build_windows(...)`.
3. Release: creates a GitHub Release tagged `v<DESCRIPTION Version>` and uploads the generated `.zip` as a release asset.
4. Safety: if the tag already exists, release creation is skipped.

Recommended repository settings:

1. Protect `main` and require pull requests for merges (this ensures releases only happen from merged PRs).
2. Keep `DESCRIPTION` version updated before merging to `main` so each merge intended for release has a unique version tag.
