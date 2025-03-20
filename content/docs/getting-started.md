---
title: Getting Started
weight: 1
---

Before you are getting started, you need something to work with. For that, you require DSC to be installed on your machine. In the following sections, you can find several ways to install it, wether running Windows, Linux or MacOS. DSC has become more cross-platform than earlier versions.

## Installing DSC for Windows from GitHub

To install DSC for Windows from GitHub, follow these steps:

### Step 1 - Determine your OS architecture

There are multiple ways to determine the operating system (OS) architecture. The following three demonstrate using the command prompt PowerShell, or msinfo.

1. Open a command prompt and type: `echo %PROCESSOR_ARCHITECTURE%`.
2. Open a PowerShell terminal session and type: `[System.Runtime.InteropServices.RuntimeInformation]::OSArchitecture`.
3. In your Quick Start menu, type in `msinfo32.exe` and locate the `System Type` property in the System Summary.

### Step 2 - Download the asset

After determining your OS architecture, proceed to download the release asset from GitHub.

1. Open the following [link](https://github.com/PowerShell/DSC/releases/) in your favorite browser.
2. Select the version you want to download and scroll-down.
3. Expand the _Assets_ and press the asset relevant to your OS architecture.

    {{< figure
      src="/images/github-download-asset.png"
      alt="GitHub download asset"
      caption="Figure 1: GitHub release assets"
    >}}

4. Save the file to your _Downloads_ folder.

### Step 3 - Expand the archive and add to PATH

When the download is finished, you can expand the files to extract them to your application data folder.

1. Right-click the file and select _Extract All..._
2. Extract the files to `C:\Users\<userProfile>\AppData\Local\dsc` by replacing the `<userProfile>` with your profile name.
3. Open a command prompt and type `rundll32.exe sysdm.cpl,EditEnvironmentVariables` to open the environment variables.
4. In your user variables, edit your `PATH` environment variable and include the path `C:\Users\<userProfile>\AppData\Local\dsc`.

### Step 4 - Verify the installation

1. Open a command prompt or PowerShell terminal session.
2. Type `dsc --version`:

  ```powershell
  dsc --version
  ```

This should return the DSC version installed.

{{< figure
      src="/images/dsc-version.png"
      alt="DSC version"
      caption="Figure 2: The DSC version installed"
>}}

> [!IMPORTANT]
> After the files are extracted, make sure the files are unblocked in your file system.

## Installing DSC for Windows using PowerShell

To install DSC for Windows using PowerShell, you can run the following options in a PowerShell terminal session.

### Option 1 - Using PSDSC module

To install DSC using the PSDSC module, follow the below steps:

1. Open a PowerShell terminal session
2. Install the `PSDSC` module by typing: `Install-PSResource -Name PSDSC`

  ```powershell
  Install-PSResource -Name PSDSC
  ```

3. Execute `Install-DscExe`
  
  ```powershell
  Install-DscExe
  ```

This installs DSC in your `"$env:LOCALAPPDAT\dsc"` folder. To verify the result, run `dsc --version`

  ```powershell
  dsc --version
  ```

> [!IMPORTANT]
> Don't confuse the `PSDSC` module for the `PSDesiredStateConfiguration`.

### Option 2 - Use PowerShell script

To install DSC using a PowerShell script, open a PowerShell terminal session and copy paste the following:

```powershell
# Define the GitHub repository and the asset pattern to download
$repo = "PowerShell/DSC"
$assetPattern = "dsc-win-.*.zip"

# Get the latest release information from GitHub API
$release = Invoke-RestMethod -Uri "https://api.github.com/repos/$repo/releases/latest"

$assetPattern = if ($env:PROCESSOR_ARCHITECTURE -eq 'ARM64')
{
  'DSC-*-aarch64-pc-windows-msvc.zip'
}
else
{
  'DSC-*-x86_64-pc-windows-msvc.zip'
}

# Find the asset that matches the pattern
$asset = $release.assets | Where-Object { $_.name -like $assetPattern }

if ($asset)
{
  # Download the asset
  $assetUrl = $asset.browser_download_url
  $downloadPath = Join-Path -Path $env:TEMP -ChildPath $asset.name
  Invoke-RestMethod -Uri $assetUrl -OutFile $downloadPath

  # Define the extraction path
  $extractPath = Join-Path -Path $env:LOCALAPPDATA -ChildPath 'dsc'

  # Create the extraction directory if it doesn't exist
  if (-not (Test-Path -Path $extractPath)) {
    New-Item -ItemType Directory -Path $extractPath
  }

  # Extract the downloaded zip file
  if (Get-Command -Name 'Expand-Archive')
  {
    Expand-Archive -Path $downloadPath -DestinationPath $extractPath -Force
  }
  else 
  {
    Add-Type -AssemblyName System.IO.Compression.FileSystem
    [System.IO.Compression.ZipFile]::ExtractToDirectory($downloadPath, $extractPath)
  }

  # Clean up the downloaded zip file
  Remove-Item -Path $downloadPath -ErrorAction SilentlyContinue

  # Unblock all files
  Get-ChildItem -Path $extractPath -Recurse | Unblock-File

  # Add to PATH
  $env:PATH += ";$extractPath"

  # Verify the installation
  dsc --version
}
```

## Installing DSC on Linux

To install DSC on Linux, follow these steps:

### Step 1 - Download the asset

1. Open a terminal session.
2. Determine your OS architecture by typing:

  ```shell
  uname -m
  ```

3. Download the DSC release asset using `wget`. Replace `<version>` with the desired version number and `<architecture>` with your OS architecture (e.g., `x86_64`):

  ```shell
  wget https://github.com/PowerShell/DSC/releases/download/v<version>/DSC-<version>-<architecture>-linux.tar.gz
  ```

### Step 2 - Extract the archive

1. Create a directory to extract the files:

  ```shell
  mkdir dsc
  ```

2. Extract the downloaded tar.gz file to the created directory:

  ```shell
  tar -xvzf DSC-<version>-<architecture>-linux.tar.gz -C dsc
  ```

### Step 3 - Move files to the appropriate location

1. Move the extracted files to `/usr/local/bin/`:

  ```shell
  sudo mv dsc/* /usr/local/bin/
  ```

### Step 4 - Update PATH environment variable in .bashrc

1. Add the DSC path to your `~/.bashrc` file:

  ```shell
  echo 'export PATH=$PATH:/usr/local/bin/dsc' >> ~/.bashrc
  ```

2. Optionally, reload the `~/.bashrc` file to apply the changes:

  ```shell
  source ~/.bashrc
  ```

### Step 5 - Verify the installation

1. Open a terminal session.
2. Type `dsc --version` to verify the installation:

  ```shell
  dsc --version
  ```

This should return the DSC version installed.

> [!NOTE]
> These steps have been tested on Ubuntu 22.04.

## Installing DSC on MacOS

To install DSC on MacOS, follow these steps:

### Step 1 - Download the asset

1. Open a terminal session.
2. Download the DSC release asset using `curl`. Replace `<version>` with the desired version number and `<architecture>` with your OS architecture (e.g., `x86_64`):

  ```shell
  curl -L https://github.com/PowerShell/DSC/releases/download/v<version>/DSC-<version>-<architecture>-apple-darwin.tar.gz -o DSC-<version>-<architecture>-apple-darwin.tar.gz
  ```

### Step 2 - Extract the archive

1. Create a directory to extract the files:

  ```shell
  mkdir dsc
  ```

2. Extract the downloaded tar.gz file to the created directory:

  ```shell
  tar -xvzf DSC-<version>-<architecture>-apple-darwin.tar.gz -C dsc
  ```

### Step 3 - Move files to the appropriate location

1. Move the extracted files to `/usr/local/bin/`:

  ```shell
  sudo mv dsc/* /usr/local/bin/
  ```

### Step 4 - Update PATH environment variable in .zshrc

1. Add the DSC path to your `~/.zshrc` file:

  ```shell
  echo 'export PATH=$PATH:/usr/local/bin' >> ~/.zshrc
  ```

2. Optionally, reload the `~/.zshrc` file to apply the changes:

  ```shell
  source ~/.zshrc
  ```

### Step 5 - Verify the installation

1. Open a terminal session.
2. Type `dsc --version` to verify the installation:

  ```shell
  dsc --version
  ```

This should return the DSC version installed.

> [!NOTE]
> These steps have been tested on MacOS 15 Sequoia.

## Next Up

You have now successfully installed DSC on your machine. Explore the following sections to start further on your journey.

{{< cards >}}
  {{< card link="../initiate/level-1-the-basics" title="The Basics" icon="document-duplicate" >}}
  {{< card link="../guide/configuration" title="Configuration" icon="adjustments" >}}
  {{< card link="../guide/markdown" title="Markdown" icon="markdown" >}}
{{< /cards >}}
