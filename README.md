<h3 align="center">Upload to Release</h3>
<p align="center">A GitHub Action that uploads a file to a new release.<p>

[![CI](https://github.com/buluma/upload-to-release/actions/workflows/main.yml/badge.svg)](https://github.com/buluma/upload-to-release/actions/workflows/main.yml)

## Usage

This action uploads any file to a new release:

<img width="926" alt="image" src="https://user-images.githubusercontent.com/10660468/49449109-2d37d400-f7a8-11e8-8e59-607c91520c96.png">

One example workflow is to build and save a Docker image then upload it to a release:

```yaml
# .github/workflows/build-docker-image.yml
name: build-docker-image

on: release

jobs:
  build-docker-image:
    name: Build and upload docker image
    runs-on: ubuntu-latest
    steps:
      - name: Pull source
        uses: actions/checkout@v1

      - name: Build Docker image
        uses: actions/docker/cli@master
        with:
          args: build . -t release-image

      - name: Save the image
        uses: actions/docker/cli@master
        with:
          args: save release-image:latest

      - name: Upload to release
        uses: buluma/upload-to-release@master
        with:
          args: release-image.tar
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Requirements

You must pass at least one argument, the path to the file you want to attach. You must also include the `GITHUB_TOKEN` secret, otherwise uploading the file will not work.

```yaml
- name: Upload to release
  uses: buluma/upload-to-release@master
  with:
    args: release-image.tar
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Content-Type

You may also need to pass an additional argument, the `Content-Type` header used when uploading your file (this is `application/zip` by default):

```yaml
- name: Upload to release
  uses: buluma/upload-to-release@master
  with:
    args: release-image.tar application/zip
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Event type

The event type your workflow is triggered from is critical to the function of this action. If you are not using `on: release` for your workflow this action will not work correctly.

The event sent to the action by GitHub only contains an artifact upload URL when using the `release` event type to trigger the workflow.

If you need other workflows not based on the `release` event, you should use multiple workflow definitions.
