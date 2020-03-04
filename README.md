## Deploy Azure DevOps agents as container apps on to Cloud Foundry

This helps solve the issue of pushing to private installation of Cloud Foundry with no public API endpoints. Instead, the model used is to push Azure DevOps worker agent on to CF itself and register them as a worker pool in Azure DevOps to be used as part of push steps in the pipeline. These connect to Azure DevOps control plane and receive worker instructions to push apps. Note that these agents only have `cf` CLI and lack most of tooling to execute common worker tasks, so they should be restricted to be used only for stages of the pipeline that do `cf push`

You can spawn more workers just by scaling the app

### Usage Instructions

1. Adjust the manifest environmental variables based on the info below

    | Env Var   | Description                                                  |
    | --- | --- |
    | CF_CLI_URL | The download url of binary version (linux x64) from [CF CLI Releases](https://github.com/cloudfoundry/cli/releases), must be in `tgz` format only. If not provided, default [v6.49.0](https://packages.cloudfoundry.org/stable?release=linux64-binary&version=6.49.0&source=github-rel) will be used |
    | AZP_URL | The URL of the Azure DevOps or Azure DevOps Server instance. Usually https://dev.azure.com/<ORG_NAME> |
    | AZP_TOKEN | Personal Access Token (PAT) granting access to `AZP_URL`. [Instructions on how to make one](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page). Should have scopes Agent Pools: Read & manage |
    | AZP_POOL | Agent pool name (default value: `Default`) |
    | AZP_WORK | Work directory (default value: `_work`) |

1. Make sure you have `exec` permission on `start.sh`, if not execute `chmod +x start.sh` before you push the agent

**NOTE: You can view/configure your pools at https://dev.azure.com/<ORG_NAME>/_settings/agentpools**
