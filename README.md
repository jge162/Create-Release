# Create-release upon closing a pull request.

Create a new release when a Pull Request is closed (with certain conditions)

![GitHub Workflow Status (with branch)](https://img.shields.io/github/actions/workflow/status/jge162/create-release/create_release.yml?branch=main&style=for-the-badge)
![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/jge162/create-release?logo=github&style=for-the-badge)
![GitHub Repo stars](https://img.shields.io/github/stars/jge162/create-release?color=red&logo=github&style=for-the-badge)


```yaml
name: Release on Pull Request Close

on:
  pull_request:
    types: [closed]

jobs:
  build-and-release:
    runs-on: ubuntu-latest
    if: github.event.pull_request.merged == true && contains(github.event.pull_request.labels.*.name, 'create release') && github.event.pull_request.user.login == 'GitHub_username_here'
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
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
        uses: softprops/action-gh-release@v1
        with:
          files: |
            build/*
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          body: |
            This is the release notes for ${{ steps.bump_version.outputs.new_tag }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```          

# License info:

jge162/create-release is licensed under the MIT License
