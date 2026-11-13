# BTP Deploy GitHub Action

This GitHub action is designed to deploy a site to SAP BTP (Business Technology Platform). It provides a set of inputs
for configuration and executes a series of steps to install the CloudFoundry CLI, login to BTP, push the manifest, and 
optionally run a Bash script.

## Inputs

- `api`: The BTP API URL. Default is 'https://api.cf.us10.hana.ondemand.com'.
- `username`: The username for API authentication. This is required.
- `password`: The password for API authentication. This is required.
- `org`: The target organization. This is optional.
- `space`: The target space. This is optional.
- `manifest`: The path to the manifest file. This is optional.
- `path`: The path to the app root directory. This is optional.
- `vars`: The manifest variables. This is optional.
- `docker_password`: The Docker password. This is optional.
- `command`: A Bash script to run after deployment. Supports multiple lines, quoting, environment variables, and pipelines. This is optional.
- `cli_version`: The cloud foundry CLI version. Default is 'v7'.
- `suppress_comments`: Set to `'true'` to skip the PR deployment comment step. Default is `'false'`.

On pull requests with a manifest, the action creates or updates a `github-actions[bot]` comment with the deployment
result, using the `manifest` input in a hidden marker (`<!-- btp-deploy:path/to/manifest.yml -->`).
Runs using the same manifest update the same comment, including failures before an application URL is available.
The calling workflow needs `pull-requests: write` permission to manage these comments.

## Usage

To use this action in a workflow, include it as a step:

```yaml
steps:
  - name: BTP Deploy
    uses: enosix/ghac-btp-deploy@stable
    with:
      api: 'https://api.cf.us10.hana.ondemand.com'
      username: ${{ secrets.BTP_USERNAME }}
      password: ${{ secrets.BTP_PASSWORD }}
      org: 'my-org'
      space: 'my-space'
      manifest: 'path/to/manifest.yml'
      vars: |
        VAR1=value1
        VAR2=value2
      docker_password: ${{ secrets.DOCKER_PASSWORD }}
      command: |
        cf apps
        cf set-env my-app ALLOWED_HOSTS 'example.com; another.example.com'
        cf restart my-app
      cli_version: 'v7'
      suppress_comments: 'false'
```

In the above example, replace `'my-org'`, `'my-space'`, `'path/to/manifest.yml'`, and `'VAR1=value1 VAR2=value2'` with 
your actual organization, space, manifest file path, and variables respectively. Also, make sure to store your BTP and 
Docker passwords as secrets in your GitHub repository.

The `command` input is optional. If provided, it runs as a Bash script after deployment (or after login if no manifest is
provided), with the CF CLI on `PATH`. It uses `-e -o pipefail`, so a failed command or pipeline fails the action.
In this example, the script lists apps, sets an environment variable containing spaces and a semicolon, and restarts the app.
Only pass trusted scripts; pass dynamic or untrusted values through environment variables and quote their expansions
inside the script rather than interpolating them into the script with GitHub expressions.
