# Trash Build Action

A GitHub Action to build static sites using the [Trash](https://github.com/zeozeozeo/trash) static site generator.

## Usage

Add this to your `.github/workflows/deploy.yml` file:

```yaml
name: Deploy Trash Site

on:
  push:
    branches:
      - main # change to the branch you want to deploy from
    # you can use `paths:` to only run on changes in set path
  workflow_dispatch:
  pull_request:
    branches:
      - main # change to the branch you want to deploy from

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: zeozeozeo/trash-build-action@v1
      - uses: actions/upload-pages-artifact@v4
        with:
          path: ./out

  deploy:
    if: github.ref == 'refs/heads/main' # change "main" to the branch you want to deploy from
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

## Inputs

| Input        | Description                              | Default   |
| ------------ | ---------------------------------------- | --------- |
| `go-version` | The version of Go to use for the build.  | `^1.25.0` |
| `build-path` | The path to the root of your Trash site. | `.`       |
