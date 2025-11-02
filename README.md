# Find Latest Docker Images Action

[![test](https://github.com/optivem/find-latest-docker-images-action/actions/workflows/test.yml/badge.svg)](https://github.com/optivem/find-latest-docker-images-action/actions/workflows/test.yml)
[![Release](https://github.com/optivem/find-latest-docker-images-action/actions/workflows/release.yml/badge.svg)](https://github.com/optivem/find-latest-docker-images-action/actions/workflows/release.yml)

A GitHub Action that resolves Docker image digests from any container registry for multiple images in batch.

## Description

This action processes multiple Docker images from any container registry (Docker Hub, GitHub Container Registry, Azure Container Registry, AWS ECR, etc.) and resolves their exact digests. Perfect for microservices architectures where you need to get digests for multiple services from various registries.

## Inputs

| Input | Description | Required | Example |
|-------|-------------|----------|---------|
| `image-urls` | Image URLs to resolve digests for. Supports both newline-separated list or JSON array format | Yes | See examples below |

### Input Formats

**Newline-separated (recommended):**
```yaml
image-urls: |
  nginx:latest
  ghcr.io/myorg/app:latest
  mcr.microsoft.com/dotnet/aspnet:8.0
```

**JSON array:**
```yaml
image-urls: '["nginx:latest", "ghcr.io/myorg/app:latest", "mcr.microsoft.com/dotnet/aspnet:8.0"]'
```

### Supported Registries

Works with any Docker-compatible registry:
- **Docker Hub**: `nginx:latest`, `ubuntu:22.04`
- **GitHub Container Registry**: `ghcr.io/owner/repo/image:latest`
- **Microsoft Container Registry**: `mcr.microsoft.com/dotnet/aspnet:8.0`
- **Azure Container Registry**: `myregistry.azurecr.io/myapp:latest`
- **AWS Elastic Container Registry**: `123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp:latest`
- **Google Container Registry**: `gcr.io/project-id/image:latest`
- **Private registries**: `my-registry.com/my-org/my-app:v1.2.3`

## Outputs

| Output | Description |
|--------|-------------|
| `image-digest-urls` | JSON array of digest URLs in the same order as input |
| `latest-image-timestamp` | Timestamp of the most recently created image among all processed images |

### Output Structure

**image-digest-urls:**
```json
[
  "nginx@sha256:abc123...",
  "ghcr.io/owner/repo/app@sha256:def456..."
]
```

**latest-image-timestamp:**
```json
"2023-12-01T10:30:00Z"
```

## Usage Examples

### Basic Example - Mixed Registries

```yaml
name: Resolve Image Digests
on: [push]

jobs:
  resolve-digests:
    runs-on: ubuntu-latest
    steps:
      - name: Resolve Docker Image Digests
        id: resolve
        uses: optivem/find-latest-docker-images-action@v1
        with:
          image-urls: |
            nginx:latest
            ghcr.io/myorg/frontend:latest
            mcr.microsoft.com/dotnet/aspnet:8.0
      
      - name: Use Resolved Digest URLs and Latest Timestamp
        run: |
          DIGESTS='${{ steps.resolve.outputs.image-digest-urls }}'
          LATEST_TIMESTAMP='${{ steps.resolve.outputs.latest-image-timestamp }}'
          
          # Access specific images by index (maintains input order)
          NGINX_DIGEST=$(echo "$DIGESTS" | jq -r '.[0]')
          FRONTEND_DIGEST=$(echo "$DIGESTS" | jq -r '.[1]')
          ASPNET_DIGEST=$(echo "$DIGESTS" | jq -r '.[2]')
          
          echo "Nginx digest URL: $NGINX_DIGEST"
          echo "Frontend digest URL: $FRONTEND_DIGEST"
          echo "ASP.NET digest URL: $ASPNET_DIGEST"
          
          # Show the latest image timestamp
          echo "Latest image timestamp: $LATEST_TIMESTAMP"
          echo "Latest image timestamp: $LATEST_TIMESTAMP"
          
          # Or iterate over all digest URLs
          echo "All digest URLs:"
          echo "$DIGESTS" | jq -r '.[]' | while read -r digest_url; do
            echo "  - $digest_url"
          done
```

### Docker Hub Images

```yaml
- name: Resolve Docker Hub Images
  uses: optivem/find-latest-docker-images-action@v1
  with:
    image-urls: |
      nginx:latest
      redis:alpine
      postgres:15
```

### GitHub Container Registry

```yaml
- name: Resolve GitHub Container Registry Images
  uses: optivem/find-latest-docker-images-action@v1
  with:
    image-urls: |
      ghcr.io/myorg/frontend:latest
      ghcr.io/myorg/backend:latest
      ghcr.io/myorg/worker:latest
```

### Current Repository Images

```yaml
- name: Resolve Current Repository Images
  uses: optivem/find-latest-docker-images-action@v1
  with:
    image-urls: |
      ghcr.io/${{ github.repository_owner }}/${{ github.event.repository.name }}/app:latest
      ghcr.io/${{ github.repository_owner }}/${{ github.event.repository.name }}/worker:latest
```

### Multiple Registries

```yaml
- name: Resolve Images from Multiple Registries
  uses: optivem/find-latest-docker-images-action@v1
  with:
    image-urls: |
      nginx:latest
      ghcr.io/myorg/app:latest
      mcr.microsoft.com/dotnet/aspnet:8.0
      myregistry.azurecr.io/myapp:latest
```

### Using JSON Array Format

```yaml
- name: Resolve Images with JSON Array
  uses: optivem/find-latest-docker-images-action@v1
  with:
    image-urls: '["nginx:latest", "ghcr.io/myorg/app:latest", "redis:alpine"]'
```

## Working with Private Registries

For private registries, make sure Docker is authenticated before running this action:

```yaml
- name: Login to GitHub Container Registry
  uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}

- name: Resolve Private Images
  uses: optivem/find-latest-docker-images-action@v1
  with:
    image-urls: |
      ghcr.io/myorg/private-app:latest
```

## Error Handling

The action uses **fail-fast behavior** - it will stop immediately on the first image that fails to resolve. This ensures:

- ✅ **Quick feedback**: Immediate failure detection
- ✅ **Resource efficiency**: Don't waste time on remaining images when one fails
- ✅ **Clear debugging**: Focus on the specific image that failed

### Common Error Scenarios

1. **Image not found**: Returns exit code 1 with clear error message
2. **Authentication required**: Ensure you're logged in to the registry
3. **Network issues**: Temporary failures will cause the action to fail
4. **Invalid image URL**: Malformed URLs will be rejected

## Why Use Digests?

Docker digests provide immutable references to specific image versions:

- **🔒 Immutable**: Digests never change, unlike tags
- **🔍 Precise**: Points to exact image content  
- **🛡️ Secure**: Prevents tag-based attacks
- **📋 Auditable**: Know exactly what's deployed

Example of using resolved digests:
```yaml
# Instead of: nginx:latest (mutable)
# Use: nginx@sha256:abc123... (immutable)
```

## Requirements

- Docker must be available in the runner environment
- For private registries, appropriate authentication must be configured
- Images must support digest resolution (most modern registries do)

## Contributing

1. Fork the repository
2. Clone your fork:
   ```bash
   git clone https://github.com/YOUR-USERNAME/find-latest-docker-images-action.git
   cd find-latest-docker-images-action
   ```

3. Make sure you have PowerShell installed for testing the script locally

4. Test the action locally by running the PowerShell script:
   ```powershell
   .\test-refactored.ps1
   ```

5. Create a Pull Request with your changes

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for a complete list of changes.
