# Create-release upon closing a pull request:

Create a new release when a Pull Request is closed (with certain conditions).

![GitHub Workflow Status (with event)](https://img.shields.io/github/actions/workflow/status/jge162/Action-Workflows/create_release.yml)
![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/jge162/create-release)
![GitHub Repo stars](https://img.shields.io/github/stars/jge162/create-release)
![GitHub package.json version](https://img.shields.io/github/package-json/v/jge162/Action-workflows)
[![Release on Pull Request Close](https://github.com/jge162/create-release/actions/workflows/create_release.yml/badge.svg)](https://github.com/jge162/create-release/actions/workflows/create_release.yml)

# Release Action:

Conditions of this release to run are a pull request must be closed and merged. 
Next you must be the repo owner and have a pull request label called `create release` chosen. 
This ensures no new releases are created unnecessary...

```yaml
name: Release on Pull Request Close

on:
  pull_request:
    types: [closed]

jobs:
  build-and-release:
    runs-on: ubuntu-latest
    if: github.event.pull_request.merged == true && contains(github.event.pull_request.labels.*.name, 'create release') && github.event.pull_request.user.login == 'jge162'
    steps:
      - name: Checkout code
        uses: jge162/Action-workflows@main
      - name: Build and test code
        run: |
          # Build and test code here
      - name: Get latest tag
        run: |
          git fetch --tags
          echo "::set-output name=latest_tag::$(git describe --tags $(git rev-list --tags --max-count=1))"
        id: get_latest_tag
      - name: Bump patch version
        run: |
          semver=$(echo "${{ steps.get_latest_tag.outputs.latest_tag }}")
          major=$(echo $semver | cut -d'.' -f1)
          minor=$(echo $semver | cut -d'.' -f2)
          patch=$(echo $semver | cut -d'.' -f3)
          new_patch=$((patch+1))
          new_tag="$major.$minor.$new_patch"
          echo "::set-output name=new_tag::$new_tag"
        id: bump_version
      - name: Create release
        uses: jge162/create-release@main
        with:
          files: |
            build/*
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          body: |
            Release has been successfully created with GitHub Action, release: ${{ steps.bump_version.outputs.new_tag }}
        env:
          GITHUB_TOKEN: ${{ secrets.WORKFLOW_SECRET }}
```          

# License info:

jge162/create-release is licensed under the [MIT License.](https://github.com/jge162/create-release/blob/main/LICENSE)
