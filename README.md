# Vintage Elasticsearch Zeabur

This repository is a minimal scaffold for deploying a custom Elasticsearch
service on Zeabur with the IK Chinese analyzer plugin.

## Files

- `docker/elasticsearch/Dockerfile`: builds Elasticsearch `9.3.3` and installs `analysis-ik`
- `zeabur.template.yaml`: Zeabur template resource for one-click deployment
- `.github/workflows/docker-publish.yml`: builds and publishes the image to GHCR

## Published Image

GitHub Actions publishes the image to:

- `ghcr.io/azul-vintage/vintage-elasticsearch-zeabur:latest`

On version tags such as `v9.3.3`, the workflow also publishes:

- `ghcr.io/azul-vintage/vintage-elasticsearch-zeabur:9.3.3`

After the first publish, set the GHCR package visibility to `Public` if you
want Zeabur to pull it without extra credentials.

## Local Build

```bash
docker build -f docker/elasticsearch/Dockerfile -t vintage-elasticsearch-zeabur:test .
```

## Deploy on Zeabur

Using the Zeabur CLI:

```bash
npx zeabur@latest template deploy -f zeabur.template.yaml
```

Or import the YAML into a Zeabur project flow that supports template files.

## Zeabur Runtime Requirements

- Keep `runAsUserID: 1000`
- Do not override the image with a root runtime user
- Persist only `/usr/share/elasticsearch/data`
- Set a strong `ELASTIC_PASSWORD`
- For Elasticsearch settings on platforms that dislike `.` in env names, use
  the official `ES_SETTING_*` form. This image already uses:
  - `ES_SETTING_DISCOVERY_TYPE`
  - `ES_SETTING_XPACK_SECURITY_ENABLED`
  - `ES_SETTING_XPACK_SECURITY_HTTP_SSL_ENABLED`
  - `ES_SETTING_XPACK_SECURITY_TRANSPORT_SSL_ENABLED`
  - `ES_SETTING_XPACK_SECURITY_AUTOCONFIGURATION_ENABLED`

## Default Connection

- Username: `elastic`
- Password: defined by `ELASTIC_PASSWORD`

If you expose Elasticsearch to the public internet, do not keep a weak password.

## Repository Layout

The Dockerfile is intentionally not stored at the repository root, so platforms
that auto-detect root-level Docker projects do not treat this repository as a
generic app service by default.
