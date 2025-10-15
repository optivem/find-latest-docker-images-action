# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed
- **BREAKING**: Repository renamed from `optivem/resolve-latest-docker-digests-action` to `optivem/find-latest-docker-images-action`
- **BREAKING**: Input parameter renamed from `images` to `image-urls` for clarity
- **BREAKING**: Output parameter renamed from `image-digests` to `image-digest-urls` for clarity
- **BREAKING**: Output format changed from dictionary to JSON array for simpler usage
- **BREAKING**: Simplified input format - now accepts array of image URLs instead of objects with repoOwner/repoName/imageName
- **BREAKING**: Action now supports any Docker registry, not just GitHub Container Registry
- Input format enhanced to support both newline-separated list and JSON array for maximum flexibility
- Users should update their workflow files to use the new repository name: `optivem/find-latest-docker-images-action@v1`
- Enhanced flexibility - works with Docker Hub, GHCR, ACR, ECR, GCR, and private registries
- Improved logging and error handling
- Fail-fast behavior - stops immediately on first failure

### Migration Guide
**Old format (v1.0.0):**
```json
[
  {
    "repoOwner": "myorg",
    "repoName": "my-repo", 
    "imageName": "my-app"
  }
]
```

**New format (v2.0.0) - Option 1 (Newline-separated, recommended):**
```yaml
image-urls: |
  ghcr.io/myorg/my-repo/my-app:latest
  nginx:latest
  mcr.microsoft.com/dotnet/aspnet:8.0
```

**New format (v2.0.0) - Option 2 (JSON array):**
```yaml
image-urls: '["ghcr.io/myorg/my-repo/my-app:latest", "nginx:latest", "mcr.microsoft.com/dotnet/aspnet:8.0"]'
```

**Output change:**
```json
// Old output (dictionary with digests)
{
  "nginx:latest": "sha256:abc123..."
}

// New output (array with digest URLs)
[
  "nginx@sha256:abc123..."
]
```

## [1.0.0] - 2024-10-09

### Added
- Initial release of the Resolve Latest Docker Digests action
- Support for resolving Docker image digests from GitHub Container Registry
- PowerShell-based implementation for cross-platform compatibility
- Comprehensive error handling and logging
- Support for public and private repositories (with proper authentication)

### Features
- Resolve exact SHA256 digests from Docker images
- Works with GitHub Container Registry (ghcr.io)
- Composite action for easy integration
- Detailed logging with emojis for better visibility
- Robust error handling with meaningful error messages

[Unreleased]: https://github.com/optivem/find-latest-docker-images-action/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/optivem/find-latest-docker-images-action/releases/tag/v1.0.0