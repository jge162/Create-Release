# Create-release upon closing a pull request:                             
              
Create a release upon closing a pull request with certain conditions:
 
![GitHub Workflow Status (with event)](https://img.shields.io/github/actions/workflow/status/jge162/Action-Workflows/create_release.yml)
![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/jge162/create-release)
![GitHub Repo stars](https://img.shields.io/github/stars/jge162/create-release)
![GitHub package.json version (subfolder of monorepo)](https://img.shields.io/github/package-json/v/jge162/create-release?filename=package.json)
![GitHub](https://img.shields.io/github/license/jge162/create-release?color=purple)
[![Create_Release](https://github.com/jge162/create-release/actions/workflows/create_release.yml/badge.svg)](https://github.com/jge162/create-release/actions/workflows/create_release.yml)

<img width="600" alt="Screenshot 2023-02-15 at 9 57 10 PM" src="https://user-images.githubusercontent.com/31228460/219280855-90b2d767-cf8c-49e8-8226-269fa190b42e.png">

# Release Action v2.1.0 below (Major release and update). 

This GitHub Workflow creates a new release whenever a Pull Request is closed under certain conditions. 
The release process has been streamlined even further with this major update. Now, to increment the version, 
you only need to type the version you want to use in the Pull Request title.


### If the Pull Request title includes "Major", the version will be incremented from 0.0.0 to 1.0.0. 
### If the title includes "Minor", the version will be incremented from 0.0.0 to 0.1.0. 
### If the title includes "Patch", the version will be incremented from 0.0.0 to 0.0.1. 

# This new process makes it much easier than creating Pull Request labels and selecting them prior to committing the Pull Request.

Please note that at the moment, only one version can be typed into the title, 
and no other words can be included other than Major, Minor, or Patch.

```yaml
# The release version will be bumped based on the Pull request title 
# for example in title Major will result in (bumps 0.0.0 to 1.0.0)
name: Create_Release

on:
  pull_request:
    types: [closed]

jobs:
  create_release:
    runs-on: ubuntu-latest
    if: github.event.pull_request.merged == true && (contains(github.event.pull_request.title, 'Major') ||            contains(github.event.pull_request.title, 'Minor') || contains(github.event.pull_request.title, 'Patch')) && github.event.pull_request.user.login == 'jge162'
    steps:  
      - name: Get PR title
        id: get_pr_title
        run: echo "::set-output name=title::${{ github.event.pull_request.title }}"

      - name: Python Action
        uses: jge162/Action-workflows@1.1.1

      - name: Get latest tag
        run: |
          git fetch --tags
          echo "::set-output name=latest_tag::$(git describe --tags $(git rev-list --tags --max-count=1))"
        id: get_latest_tag

      - name: Bump version
        run: |
          semver=$(echo "${{ steps.get_latest_tag.outputs.latest_tag }}")
          major=$(echo $semver | cut -d'.' -f1)
          minor=$(echo $semver | cut -d'.' -f2)
          patch=$(echo $semver | cut -d'.' -f3)
          if [[ "${{ steps.get_pr_title.outputs.title }}" == *"Major"* ]]; then
            new_major=$((major+1))
            new_tag="$new_major.$minor.$patch"
          elif [[ "${{ steps.get_pr_title.outputs.title }}" == *"Minor"* ]]; then
            new_minor=$((minor+1))
            new_tag="$major.$new_minor.$patch"
          else
            new_patch=$((patch+1))
            new_tag="$major.$minor.$new_patch"
          fi
          echo "::set-output name=new_tag::$new_tag"
        id: bump_version

      - name: create-release-on-close
        uses: jge162/create-release@v1.1.0
  
        with:
          files: |
            build/*
            
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          prerelease: Release ${{ steps.bump_version.outputs.new_tag }}
          body: |
            This is a new release ${{ steps.bump_version.outputs.new_tag }} which will include the following changes: ${{ steps.get_pr_title.outputs.title }}
        env:
          GITHUB_TOKEN: ${{ secrets.WORKFLOW_SECRET }}
```

## License info:

jge162/create-release is licensed under the [MIT License](https://github.com/jge162/create-release/blob/main/LICENSE)
