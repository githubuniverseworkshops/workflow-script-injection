# Exercise 4 - Enhance the detection of vulnerabilities using third party queries

## Unpinned Actions
The individual jobs in a GitHub Actions workflow can interact with (and compromise) other jobs. For example, a job querying the environment variables used by a later job, writing files to a shared directory that a later job processes, or even more directly by interacting with the Docker socket and inspecting other running containers and executing commands in them. This means that a compromise of a single action within a workflow can be very significant, as that compromised action would have access to all secrets configured on your repository, and may be able to use the GITHUB_TOKEN to write to the repository. Consequently, there is significant risk in sourcing actions from third-party repositories on GitHub. For information on some of the steps an attacker could take, see [Security hardening for GitHub Actions.](https://docs.github.com/en/enterprise-cloud@latest/actions/security-guides/security-hardening-for-github-actions#using-third-party-actions)

GitHub's Field Team's has built Custom CodeQL Queries, Suites, and Configurations to address several vulnerabilities and they have a query for `CWE-829` to detect Unpinned Actions. The queries are part of the https://github.com/advanced-security/codeql-queries/ repo. 

In our exercise we have included a workflow file called `unpinned-action.yml` that has this vulnerability and our goal is to detect this vulnerability using the advanced security queries developed by the GitHub field team.

### :keyboard: Activity: Create a code scanning config file.
A custom configuration file is an alternative way to specify additional packs and queries to run. You can also use the file to disable the default queries, exclude or include specific queries, and to specify which directories to scan during analysis. See [Using a custom configuration file](https://docs.github.com/en/code-security/code-scanning/creating-an-advanced-setup-for-code-scanning/customizing-your-advanced-setup-for-code-scanning#using-a-custom-configuration-file)

In this workshop we will customize to:
- Add one addtional pack `advanced-security/codeql-javascript`
- Add a path filter to only look at the workflows
- Add a query filter to only use the Actions specific queries.
> **NOTE**    
> The last two customizations are to improve the performance by reducing the codebase and using a narrow set of queries.

  
1. Create a config file in the root of the repository with the following name `codeql-config.yml`. Add the following contents in the file:
```
packs:
  # Use the latest version of 'codeql-javascript' published by 'advanced-security'
  - advanced-security/codeql-javascript
query-filters:
  - include:
      tags contain: actions
paths:
  - '.github/workflows'
```

### :keyboard: Activity: Update the workflow.

2. In the workflow file, use the config-file parameter of the init action to specify the path to the configuration file you want to use. In our exercise we load the configuration file `./codeql-config.yml`. The modified workflow file should look like this (comments have been removed for readability):

```
name: "Actions Workflow CodeQL"

on:
  push:
    branches: [ "develop" ]
  workflow_dispatch:

jobs:
  analyze:
    name: Analyze
    runs-on: 'ubuntu-latest'
    timeout-minutes: 360
    permissions:
      actions: read
      contents: read
      security-events: write

    strategy:
      fail-fast: false
      matrix:
        language: [ 'javascript' ]

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    # Initializes the CodeQL tools for scanning.
    - name: Initialize CodeQL
      uses: github/codeql-action/init@v2
      with:
        languages: ${{ matrix.language }}
        config-file: './codeql-config.yml'
    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v2
      with:
        category: "/language:${{matrix.language}}"
```

3. When the file is committed, the `Actions WorkFlow CodeQL` workflow should be triggered. Once this is completed, check the `security` tab to see the alerts for the new vulnerability.

