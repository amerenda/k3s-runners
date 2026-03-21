# k3s-runners

Custom Docker images used as GitHub Actions runner containers for the k3s home lab cluster. Images are published to Docker Hub under `amerenda/k3s-runners:<tag>`.

## Available Images

| Directory | Docker Hub Image | Base | Description |
|-----------|-----------------|------|-------------|
| `home-assistant/` | `amerenda/k3s-runners:home-assistant` | `python:3.12-slim` | Python 3.12 with yamllint, jinja2-cli for HA config validation |
| `kaniko/` | `amerenda/k3s-runners:kaniko` | `node:20-slim` | Node.js 20 with Kaniko executor for in-cluster Docker builds |

### home-assistant

Pre-installed tools for Home Assistant configuration CI:
- `yamllint` -- YAML linting
- `jinja2-cli` / `jinja2` -- Template processing
- `git`, `curl`, `bash`

### kaniko

Combines Node.js 20 with the [Kaniko](https://github.com/GoogleContainerTools/kaniko) executor, enabling Docker image builds inside Kubernetes pods without Docker-in-Docker. Used by application repos (ecdysis, llm-agents, llm-manager) to build and push images from ARC runner pods.

Includes:
- `/kaniko/executor` -- Kaniko build tool
- `git`, `curl` -- For checkout and API calls
- Node.js 20 runtime -- For npm-based builds

## Adding a New Runner Image

1. Create `<name>/Dockerfile` with the tools needed for your CI jobs.
2. Create `.github/workflows/<name>-build.yaml` (copy an existing workflow and update the image name, path filter, and tags).
3. Reference the image in your repo's workflow:

```yaml
jobs:
  build:
    runs-on: arc-runner-set
    container:
      image: amerenda/k3s-runners:<name>
```

4. Open a PR. CI will validate the Docker build.

## CI/CD

Each image has its own workflow in `.github/workflows/` that triggers on changes to that image's directory.

- **Trigger:** Push to `main` affecting `<name>/**` or the workflow file
- **Runner:** `ubuntu-latest` (GitHub-hosted, Docker pre-installed)
- **Platforms:** `linux/amd64` + `linux/arm64` (multi-arch for x86 dev machines and RPi cluster nodes)
- **Registry:** Docker Hub (`amerenda/k3s-runners:<name>`)

### Required Secrets

| Secret | Description |
|--------|-------------|
| `DOCKERHUB_USERNAME` | Docker Hub username (`amerenda`) |
| `DOCKERHUB_TOKEN` | Docker Hub personal access token (read/write) |
