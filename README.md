# BTP Deploy GitHub Action

This GitHub action is designed to deploy a site to SAP BTP (Business Technology Platform). It provides a set of inputs
for configuration and executes a series of steps to install the CloudFoundry CLI, login to BTP, push the manifest, and 
optionally run a specified command.

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
- `command`: A command to run after deployment. This is optional.
- `cli_version`: The cloud foundry CLI version. Default is 'v7'.

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
      cli_version: 'v7'
```

In the above example, replace `'my-org'`, `'my-space'`, `'path/to/manifest.yml'`, and `'VAR1=value1 VAR2=value2'` with 
your actual organization, space, manifest file path, and variables respectively. Also, make sure to store your BTP and 
Docker passwords as secrets in your GitHub repository.

The `command` input is optional. If provided, the command will be executed after the deployment. In this example, 
`cf apps` is used to list all the apps in the target space after the deployment.
