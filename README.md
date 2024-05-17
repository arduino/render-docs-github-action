# 📖 render-docs-github-action

This repository contains the Github Action for the `render-docs` command line tool. The purpose of it is to create markdown documentation from doxygen compatible code comments in C and C++ code.
The benefit of maintaining API documentation along with the code itself is that you get inline documentation tooltips by IDEs that support that plus the chance of documentation getting out of sync is lower as code maintainers have it in front of their eyes when making changes.
The tool uses a fork of `moxygen` to parse the XML output generated by `doxygen`.
Rendering the documentation as part of the CI pipeline ensures always up-to-date documentation.

## 💻 Usage

This Github workflow can be imported into your own workflow via `uses` property: 
`arduino/render-docs-github-action/.github/workflows/render-docs.yml`
The minimal arguments that it needs are `source-path` and `target-path`.

Here is a minimal workflow to render documentation in your own repository:

```yaml
name: Render Documentation

on:
  push:
    branches:
      - main
    paths:
      - ".github/workflows/render-documentation.ya?ml"
      - "examples/**"
      - "src/**"
  pull_request:
    branches:
      - main
    paths:
      - ".github/workflows/render-documentation.ya?ml"
      - "examples/**"
      - "src/**"
  workflow_dispatch:
      
jobs:
  render-docs:
    permissions:
      contents: write
    uses: arduino/render-docs-github-action/.github/workflows/render-docs.yml@main
    with:
      source-path: './src'
      target-path: './docs/api.md'
      commit: ${{ github.event_name != 'pull_request' }} # Only commit changes if not a PR
```

You can also run the tool as a Github action, rather than a reusable workflow:

```yaml
jobs:
  render-docs:
    permissions:
      contents: write

    steps:
    - uses: actions/checkout@v4
    - uses: arduino/render-docs-github-action@main
      with:
        source-path: './src'
        target-path: './docs/api.md'
```

Please note that this workflow only triggers when files are modified that match the given `paths:` pattern. This can be useful to avoid unnecessary workflow runs.
Also the workflow needs to be granted write permissions to commit the rendered documentation file. This is unless the `commit` option is set to false. To grand write permissions you can either enable this in the settings for the repository for any workflow that uses the default Github token, or, as shown here using:
```
permissions:
      contents: write
```

### Inputs

The workflow has a couple of inputs that allows to configure the behaviour:

- `source-path` The source path where the action should look for code documentation in .h files.
- `target-path` The target path of the markdown file e.g. ./docs/api.md
- `exclude-pattern` Use this when certain files or folder inside the source folder should not be processed.
- `include-cpp` It's good practice to do the class and function declarations in the .h file and thus also add the documentation there. However, if you happen to have code documentation also in .cpp files enabling this option will process those files too.
- `show-access-modifiers` If set to true, the access modifiers will be rendered in the documentation.
- `access-level` Allows to specify which class members to filter out in the documentation. If you specify `private` all members will show up in the documentation. If you set it to `protected` only the private members will be filtered out. When set to `public` only public members will be processed.
- `fail-on-warnings` The command prints documentation issues such as missing documentation. This option makes the action fail when such issues are found. This allows to achieve 100% documentation coverage.
- `commit` Defines whether the rendered markdown files should be committed to the repository automatically.
- `commit-message` Defines the commit message to be used when `commit` is set to true.
- `debug` Enable this option to get additional output from the tool. This allows to debug in case you get unexpected output from the command.

## 🐛 Reporting Issues

If you encounter any issue, please open a bug report [here](https://github.com/arduino/render-docs-github-action/issues). 

## 💪 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## 🤙 Contact

For questions, comments, or feedback on this package, please create an issue on this repository.