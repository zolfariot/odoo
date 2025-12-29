# Odoo Docker Multi-Platform Build

This repository contains a Dockerized Odoo setup based on [Doodba](https://github.com/Tecnativa/doodba) with automated multi-platform builds for AMD64 and ARM64 architectures.

## 🚀 Features

- **Multi-platform support**: Builds for `linux/amd64` and `linux/arm64`
- **Automated builds**: GitHub Actions automatically build and push images to GitHub Container Registry
- **Configurable Odoo version**: Easy version management via build arguments
- **Smart tagging**: Automatic image tagging based on branches, tags, and versions

## 📦 Docker Image

The built Docker images are available at:

```
ghcr.io/zolfariot/odoo:latest
ghcr.io/zolfariot/odoo:18
ghcr.io/zolfariot/odoo:main
```

## 🔧 Setup

### Prerequisites

- GitHub account with access to GitHub Container Registry (GHCR)
- Repository secrets configured (automatic for public repos)

### GitHub Configuration

1. **Enable GitHub Actions**: Ensure GitHub Actions is enabled in your repository settings

2. **Package Permissions**: 
   - Go to your repository's **Settings** → **Actions** → **General**
   - Under "Workflow permissions", ensure "Read and write permissions" is selected

3. **Container Registry Access**:
   - The workflow uses `GITHUB_TOKEN` which is automatically provided
   - No additional secrets needed for GHCR access!

## 🏗️ Building Images

### Automatic Builds

The workflow automatically builds and pushes images when:

- **Push to `main` branch**: Creates `latest` and `main` tags
- **Push to `develop` branch**: Creates `develop` tag
- **Push tags matching `v*`**: Creates version tags (e.g., `v1.0.0`, `1.0`, `1`)
- **Pull requests**: Builds but doesn't push (for testing)

### Manual Builds

Trigger a manual build with custom Odoo version:

1. Go to **Actions** tab in GitHub
2. Select **Build and Push Docker Image**
3. Click **Run workflow**
4. Enter the desired Odoo version (e.g., `18.0`, `17.0`)
5. Click **Run workflow**

### Local Builds

Build locally with Docker Buildx:

```bash
# Build for multiple platforms
docker buildx build \
  --platform=linux/amd64,linux/arm64 \
  -t ghcr.io/zolfariot/odoo:18 \
  --build-arg ODOO_VERSION=18.0 \
  --push \
  .

# Build for current platform only (faster for testing)
docker build \
  -t ghcr.io/zolfariot/odoo:18 \
  --build-arg ODOO_VERSION=18.0 \
  .
```

## 🐳 Using the Image

Pull and run the image:

```bash
# Pull the latest image
docker pull ghcr.io/zolfariot/odoo:latest

# Run Odoo container
docker run -d \
  --name odoo \
  -p 8069:8069 \
  -v odoo-data:/var/lib/odoo \
  ghcr.io/zolfariot/odoo:18
```

## 📁 Repository Structure

```
.
├── .github/
│   └── workflows/
│       └── docker-build-push.yml  # GitHub Actions workflow
├── custom/
│   ├── src/                       # Custom addons and repos
│   ├── conf.d/                    # Configuration files
│   ├── dependencies/              # Dependency lists
│   └── ssh/                       # SSH keys (gitignored)
├── Dockerfile                     # Docker image definition
├── .gitignore                     # Git ignore rules
└── README.md                      # This file
```

## 🔐 Security Notes

- SSH keys in `custom/ssh/` are excluded from git by `.gitignore`
- Never commit sensitive files like private keys or `.env` files
- The `GITHUB_TOKEN` is automatically scoped to your repository

## 🛠️ Workflow Details

The GitHub Actions workflow:

1. **Checks out** the repository code
2. **Sets up QEMU** for multi-platform emulation
3. **Configures Docker Buildx** for advanced builds
4. **Authenticates** to GitHub Container Registry
5. **Extracts metadata** for smart tagging
6. **Builds** the Docker image for AMD64 and ARM64
7. **Pushes** to GHCR (except for PRs)
8. **Caches** layers for faster subsequent builds

### Available Tags

- `latest`: Latest build from main branch
- `main`: Latest main branch build
- `develop`: Latest develop branch build
- `18`: Specific Odoo version
- `<branch>-<sha>`: Commit-specific tags
- `v1.0.0`, `1.0`, `1`: Semantic version tags

## 🔄 Updating Odoo Version

To change the default Odoo version, edit the `ODOO_VERSION` argument in the `Dockerfile`:

```dockerfile
ARG ODOO_VERSION=18.0
FROM ghcr.io/tecnativa/doodba:${ODOO_VERSION}-onbuild
```

Commit and push to trigger a new build.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📝 License

[Add your license information here]

## 🔗 Related Resources

- [Doodba Documentation](https://github.com/Tecnativa/doodba)
- [Odoo Official Documentation](https://www.odoo.com/documentation)
- [Docker Buildx Documentation](https://docs.docker.com/buildx/working-with-buildx/)
- [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
