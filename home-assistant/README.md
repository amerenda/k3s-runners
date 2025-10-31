# Home Assistant Runner Docker Image

This Docker image provides a pre-configured environment with Python dependencies needed for Home Assistant configuration workflows. It includes `yamllint`, `jinja2-cli`, and other utilities pre-installed.

## What's Included

- **yamllint** - YAML linting tool
- **jinja2-cli** - Jinja2 template processing
- **jinja2** - Jinja2 templating library
- **git**, **curl**, **bash** - System utilities

**Note:** For Home Assistant configuration validation and hassfest checks, use the official GitHub Actions:
- `frenck/action-home-assistant@v1` - For HA config validation
- `home-assistant/actions/hassfest@master` - For hassfest validation

## Usage

### Building the Image

```bash
docker build -t amerenda/k3s-runners:home-assistant ./home-assistant
```

### Using in GitHub Actions

This image is designed to be used for YAML linting and Jinja2 template processing. For Home Assistant-specific validation, use the official actions.

#### Running yamllint

```bash
docker run --rm \
  -v $(pwd)/gitops/apps/home-assistant/configuration:/workspace/config:ro \
  amerenda/k3s-runners:home-assistant \
  yamllint -c .yamllint.yaml /workspace/config/
```

#### Processing Jinja2 Templates

```bash
docker run --rm \
  -v $(pwd)/gitops/apps/home-assistant:/workspace:ro \
  amerenda/k3s-runners:home-assistant \
  jinja2 template.yaml.j2 data.yaml
```

## Benefits

1. **Lightweight** - Only includes essential Python dependencies
2. **Fast builds** - Minimal dependencies mean quick image builds
3. **Consistent environment** - Same environment across all runs
4. **Reusable** - Can be used for any YAML/Jinja2 processing tasks

## Building and Pushing

The image is automatically built and pushed to Docker Hub when changes are made to the `home-assistant/` directory or the build workflow. See `.github/workflows/home-assistant-build.yaml` for details.

Manual build and push:

```bash
docker build -t amerenda/k3s-runners:home-assistant ./home-assistant
docker push amerenda/k3s-runners:home-assistant
```

## Recommended Workflow Setup

For complete Home Assistant validation, use this image alongside the official actions:

```yaml
jobs:
  yaml-lint:
    runs-on: arc-runner-set
    steps:
      - uses: actions/checkout@v4
      - name: Run yamllint
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace:ro \
            amerenda/k3s-runners:home-assistant \
            yamllint -c .yamllint.yaml gitops/apps/home-assistant/configuration/

  hassfest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: home-assistant/actions/hassfest@master

  ha-config-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: frenck/action-home-assistant@v1
        with:
          version: "2024.10.0"
          path: /tmp/ha-config
```
