# WSL Docker Images Builder

A GitHub Action workflow that automatically builds and maintains Docker images based on the official Windows Subsystem for Linux (WSL) distributions.

## Overview

This project monitors the official [Microsoft WSL DistributionInfo.json](https://github.com/microsoft/WSL/blob/master/distributions/DistributionInfo.json) file for changes and automatically builds Docker images for all available WSL distributions. The built images are pushed to GitHub Container Registry (ghcr.io) and can be used as base images for custom WSL environments.

## Features

- **Automatic Updates**: Daily checks for changes to the official WSL distribution list
- **Version Tagging**: Images are tagged with their distribution version, date, and 'latest'
- **Multi-Arch Support**: Builds images for AMD64 architecture (ARM64 support can be enabled just not currently implemented)
- **GitHub Container Registry Integration**: All images are pushed to ghcr.io
- **Efficient Caching**: Only rebuilds images when distribution files change

## Available Images

Images are published to GitHub Container Registry with the following naming convention:

```
ghcr.io/<username>/<distro-name>-wsl:<tag>
```

For example:
- `ghcr.io/stevenbuglione/ubuntu-wsl:24.04`
- `ghcr.io/stevenbuglione/ubuntu-wsl:latest`
- `ghcr.io/stevenbuglione/kali-linux-wsl:2024.4`

## How It Works

1. **Monitoring**: A GitHub Action runs daily to check if Microsoft's DistributionInfo.json has been updated
2. **Detection**: Using content hashing to identify changes in the upstream file
3. **Processing**: When changes are detected, the workflow:
    - Downloads the distribution tarballs
    - Extracts version information
    - Imports them as Docker images
    - Tags images with version, date, and 'latest'
    - Pushes all images to GitHub Container Registry

## Usage

### Pull an Image

```bash
docker pull ghcr.io/stevenbuglione/ubuntu-wsl:latest
```

### Use as a Base Image

```dockerfile
FROM ghcr.io/stevenbuglione/ubuntu-wsl:24.04
RUN apt-get update && apt-get install -y nginx
# Additional customization...
```

### Run the Image

```bash
docker run -it ghcr.io/stevenbuglione/ubuntu-wsl:latest bash
```

## Setup Your Own Builder

1. **Fork the Repository**

2. **Configure GitHub Secrets**:
    - Ensure `GITHUB_TOKEN` has packages write permission

3. **Customize the Workflow** (Optional):
    - Modify `.github/workflows/build-wsl-images.yml` to select specific distributions or architectures

## Development

### Requirements

- Go 1.21 or later
- Docker
- GitHub Actions

### Local Testing

```bash
# Clone the repository
git clone https://github.com/yourusername/wsl-docker-images.git
cd wsl-docker-images

# Run the builder locally
go run main.go
```

## GitHub Action Workflow

The workflow runs:
- Daily at midnight (UTC)
- When manually triggered

The workflow consists of two main jobs:
1. `check-for-changes`: Monitors the DistributionInfo.json file for changes
2. `build-and-push`: Builds and pushes Docker images if changes are detected


## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.