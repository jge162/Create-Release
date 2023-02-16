# Create-release upon closing a pull request:                             
             
Create a new release when a Pull Request is closed (with certain conditions).                  
 
![GitHub Workflow Status (with event)](https://img.shields.io/github/actions/workflow/status/jge162/Action-Workflows/create_release_minor.yml)
![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/jge162/create-release)
![GitHub Repo stars](https://img.shields.io/github/stars/jge162/create-release)
![GitHub package.json version (subfolder of monorepo)](https://img.shields.io/github/package-json/v/jge162/create-release?filename=package.json)
![GitHub](https://img.shields.io/github/license/jge162/create-release?color=purple)
[![create_release_major_](https://github.com/jge162/create-release/actions/workflows/create_release_major.yml/badge.svg)](https://github.com/jge162/create-release/actions/workflows/create_release_major.yml)
[![create_release_minor_](https://github.com/jge162/create-release/actions/workflows/create_release_minor.yml/badge.svg)](https://github.com/jge162/create-release/actions/workflows/create_release_minor.yml)
[![create_release_patch_](https://github.com/jge162/create-release/actions/workflows/create_release_patch.yml/badge.svg)](https://github.com/jge162/create-release/actions/workflows/create_release_patch.yml)

<img width="600" alt="Screenshot 2023-02-15 at 9 57 10 PM" src="https://user-images.githubusercontent.com/31228460/219280855-90b2d767-cf8c-49e8-8226-269fa190b42e.png">

# Release Action v0.0.0 below (beta). 

Conditions of this release to run are a pull request must be closed and merged. 
Next you must be the repo owner and have a pull request label called `create release` chosen. 
This ensures no new releases are created unnecessary.

Step1: create a new directory `./build/realease_notes.txt` in this follder put your release notes which will be
uploaded to your release as attachment that can be downloaded. 
 
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
      - name: Python Action
        uses: jge162/Action-workflows@1.0.1
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
      - name: create-release-on-close
        uses: jge162/create-release@v1.1.1
        with:
          files: |
            build/*
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          body: |
            Release has been successfully created with GitHub Action, release: ${{ steps.bump_version.outputs.new_tag }}
        env:
          GITHUB_TOKEN: ${{ secrets.WORKFLOW_SECRET }}
```          
# Release of 'Major' version push supported in update `v1.0.0`

In this version release candidates v1.0.0 (major). Each time a pull request is closed, depending on if you chose, major, minor or patch release. 
The selected version will increment by +1. 

In this code snippet below, you will be able to push a major releae update, for example bump
from v0.0.0 to v1.0.0 onwards per major release created only incrementing major v1.0.0 to v2.0.0 and v3.0.0.

You must create a Label called 'create release major' and GitHub user == <owner> to <owner> and pull request `merge` == true,
as conditions for release Action to run. This prevents all other users from creating releases. 
  
See example below: 

```yaml
jobs:
  build-and-release:
    runs-on: ubuntu-latest
    if: github.event.pull_request.merged == true &&
    contains(github.event.pull_request.labels.*.name, 'create release major') &&
    github.event.pull_request.user.login == 'jge162'
    steps:
```

Here you can see how the **Major** bump occurs and does not affect, minor or patch versions.
  
```yaml
 - name: Bump major version
        run: |
          semver=$(echo "${{ steps.get_latest_tag.outputs.latest_tag }}")
          major=$(echo $semver | cut -d'.' -f1)
          minor=$(echo $semver | cut -d'.' -f2)
          patch=$(echo $semver | cut -d'.' -f3)
          new_major=$((major+1))
          new_tag="$new_major.$minor.$patch"
          echo "::set-output name=new_tag::$new_tag"
        id: bump_version

      - name: Create release
        uses: actions/create-release@v1
        files: |
            build/*
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          prerelease: Release ${{ steps.bump_version.outputs.new_tag }}
          body: This is a MAJOR release ${{ steps.bump_version.outputs.new_tag }} which will include feature updates.
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


# Release of 'MINOR' version push supported in update `v1.1.0`

In this version release candidates v1.1.0 (minor). Each time a pull request is closed, depending on if you chose, major, minor or patch release. 
The selected version will increment by +1. 

In this code snippet below, you will be able to push a 'minor' releae update, for example bump
from v1.0.0 to v1.1.0 onwards per major release created only incrementing 'minor' v1.1.0 to v1.2.0 and v1.3.0.

You must create a Label called 'create release minor' and GitHub user == <owner> to <owner> and pull request `merge` == true,
as conditions for release Action to run. This prevents all other users from creating releases. 
  
See example below: 

```yaml
jobs:
  build-and-release:
    runs-on: ubuntu-latest
    if: github.event.pull_request.merged == true &&
    contains(github.event.pull_request.labels.*.name, 'create release minor') &&
    github.event.pull_request.user.login == 'jge162'
    steps:
```

Here you can see how the **Major** bump occurs and does not affect, minor or patch versions.
  
```yaml
 - name: Bump minor version
        run: |
          semver=$(echo "${{ steps.get_latest_tag.outputs.latest_tag }}")
          major=$(echo $semver | cut -d'.' -f1)
          minor=$(echo $semver | cut -d'.' -f2)
          patch=$(echo $semver | cut -d'.' -f3)
          new_minor=$((minor+1))
          new_tag="$major.$new_minor.$patch"
          echo "::set-output name=new_tag::$new_tag"
        id: bump_version

      - name: Create release
        uses: actions/create-release@v1
        files: |
            build/*
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          prerelease: Release ${{ steps.bump_version.outputs.new_tag }}
          body: This is a MINOR release ${{ steps.bump_version.outputs.new_tag }} which will include bug fixes and minor updates.
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

# Release of 'PATCH' version push supported in update `v1.1.1`

In this version release candidates v1.1.1 (Patch). Each time a pull request is closed, depending on if you chose, major, minor or patch release. 
The selected version will increment by +1. 

In this code snippet below, you will be able to push a 'patch' releae update, for example bump
from v0.0.0 to v0.0.2 onwards per major release created only incrementing 'patch' v1.1.1 to v1.1.2 and v1.1.3.

You must create a Label called 'create release patch' and GitHub user == 'owner' to <owner> and pull request `merge` == true,
as conditions for release Action to run. This prevents all other users from creating releases. 
  
See example below:    

```yaml
jobs:
  build-and-release:
    runs-on: ubuntu-latest
    if: github.event.pull_request.merged == true &&
    contains(github.event.pull_request.labels.*.name, 'create release patch') &&
    github.event.pull_request.user.login == 'jge162'
    steps:
```

Here you can see how the **Major** bump occurs and does not affect, minor or patch versions.
  
```yaml
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
        uses: actions/create-release@v1
        files: |
            build/*
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          prerelease: Release ${{ steps.bump_version.outputs.new_tag }}
          body: This is a PATCH release ${{ steps.bump_version.outputs.new_tag }} which will include bug fixes.
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

# Feature release of version v2.1.1 (major, minor, patch).

This example below displays full code running (major) just replace `bumper major version`
as need depending on which version you are looking to update -> `(major, minor, patch)`. 

```yaml
name: create_release_major_
# release verison is patch v1.0.0

on:
  pull_request:
    types: [closed]

jobs:
  create_release_major:
    runs-on: ubuntu-latest
    if: github.event.pull_request.merged == true && contains(github.event.pull_request.labels.*.name, 'create release major') && github.event.pull_request.user.login == 'jge162'
    steps:

      - name: Python Action
        uses: jge162/Action-workflows@1.0.1

      - name: Build major release
        run: |
          # Build and test code here

      - name: Get latest tag
        run: |
          git fetch --tags
          echo "::set-output name=latest_tag::$(git describe --tags $(git rev-list --tags --max-count=1))"
        id: get_latest_tag

      - name: Bump major version
        run: |
          semver=$(echo "${{ steps.get_latest_tag.outputs.latest_tag }}")
          major=$(echo $semver | cut -d'.' -f1)
          minor=$(echo $semver | cut -d'.' -f2)
          patch=$(echo $semver | cut -d'.' -f3)
          new_major=$((major+1))
          new_tag="$new_major.$minor.$patch"
          echo "::set-output name=new_tag::$new_tag"
        id: bump_version

      - name: create-release-on-close
        uses: jge162/create-release@2.1.1
  
       files: |
            build/*
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          prerelease: Release ${{ steps.bump_version.outputs.new_tag }}
          body: This is a new release ${{ steps.bump_version.outputs.new_tag }} which will include feature updates.
        env:
          GITHUB_TOKEN: ${{ secrets.WORKFLOW_SECRET }}
```

## License info:

jge162/create-release is licensed under the [MIT License](https://github.com/jge162/create-release/blob/main/LICENSE)
