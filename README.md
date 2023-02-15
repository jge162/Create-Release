# Create-release upon closing a pull request:

Create a new release when a Pull Request is closed (with certain conditions).

![GitHub Workflow Status (with event)](https://img.shields.io/github/actions/workflow/status/jge162/Action-Workflows/create_release.yml)
![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/jge162/create-release)
![GitHub Repo stars](https://img.shields.io/github/stars/jge162/create-release)
![GitHub package.json version](https://img.shields.io/github/package-json/v/jge162/Action-workflows)

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

# Here you can see how the **Major** bump occurs and does not affect, minor or patch versions.
  
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
        with:
          tag_name: ${{ steps.bump_version.outputs.new_tag }}
          release_name: Release ${{ steps.bump_version.outputs.new_tag }}
          body: This is a MAJOR release ${{ steps.bump_version.outputs.new_tag }} which will include feature updates.
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

# Future releases to expect soon are v1.1.0 (minor) and v1.1.1 (patch).

These will allow you to increment each release based on whether it's a major, minor or patch release. 
For example, can increment v1.0.0 to v1.1.0 then into v1.1.1 after new releases. 

# License info:

jge162/create-release is licensed under the [MIT License](https://github.com/jge162/create-release/blob/main/LICENSE)
