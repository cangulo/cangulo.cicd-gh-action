################################################
# DO NOT USE ME - THIS PROJECT IS NOT READY YET
################################################

# cangulo.cicd-gh-action

## What cicd operations I can do?

* All the dotnet operations as test, build and package
* Create Release with assets
* Compress Directories

For the full list check the [cangulo.cicd](https://github.com/cangulo/cangulo.cicd#available-actions) repository.

## How to use me?

1. Define a cicd.json in the root of your project. 
2. Add the next json schema property to it.

```json
{
    "$schema": "https://raw.githubusercontent.com/cangulo/cangulo.cicd/main/cicd.schema.json"
}
```

3. Depending on which CI actions you want to execute, add the properties required. Please refer to [cangulo.cicd docs](https://github.com/cangulo/cangulo.cicd#available-actions) for required properties per action.
4. Defined you github action in yml as the next example, after the properties --target placed the CI action you want to execute:

### Download latest or specific version

```yml
name: Testing cangulo.cicd

on: [push]

jobs:
  hello_world_job:
    runs-on: ubuntu-latest
    name: Downloading cangulo.cicd
    steps:
      - name: Download latest cangulo.cicd
        uses: cangulo/cangulo.cicd-gh-action@main
        with:
          version: latest
      - name: Display help of the latest version
        run: ./cangulo.cicd/cangulo.cicd --root . --help --verbosity verbose
      - name: Download cangulo.cicd version 0.0.18
        uses: cangulo/cangulo.cicd-gh-action@main
        with:
          version: 0.0.18
      - name: Display help of the version 0.0.18
        run: ./cangulo.cicd/cangulo.cicd --root . --help --verbosity verbose
```

### One target

```yml
name: Execute UT

on: [push]

jobs:
  hello_world_job:
    runs-on: ubuntu-latest
    name: Execute UT using cangulo.cicd
    steps:
      - uses: actions/checkout@v1
      - name: Downloading cangulo.cicd
        uses: cangulo/cangulo.cicd-gh-action@v0.0.1
        with:
          version: latest
      - name: Execute UT
        run: ./cangulo.cicd/cangulo.cicd --root . --target ExecuteUnitTests

```
### Multiple targets
```yml
name: PR Merged to main

on:
  pull_request:
    types:
      - closed
    branches:
      - main

jobs:
  ubuntu-latest:
    name: ubuntu-latest
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Downloading cangulo.cicd
        uses: cangulo/cangulo.cicd-gh-action@v0.0.1
        with:
          version: latest
      - name: Calculate Next Release Number, Update Version In Files, Push Changes
        env:
          GitHubToken: ${{ secrets.GITHUB_TOKEN }}
        run: ./cangulo.cicd/cangulo.cicd --root . --target  CalculateNextReleaseNumber UpdateVersionInFiles GitPush
      - name: Dotnet Publish and Zip the assets
        run: ./cangulo.cicd/cangulo.cicd --root . --target  Publish CompressDirectory
      - name: Create the New Release
        env:
          GitHubToken: ${{ secrets.GITHUB_TOKEN }}
        run: ./cangulo.cicd/cangulo.cicd --root . --target  CreateNewRelease
```


## Examples:

| Repo                                                              | GitHub Action                                                                                         |
| ----------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| [cangulo.changelog](https://github.com/cangulo/cangulo.changelog) | [Execute UT ](https://github.com/cangulo/cangulo.changelog/actions/workflows/testingcangulobuild.yml) |
