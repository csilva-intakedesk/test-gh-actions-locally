# Testing GitHub Actions Locally

GitHub Action Local test. Locally testing the actions will help with prevent costs with GitHub and prevent several deployments to see if is actually working.

## Requirements:

* node
* npm
* git
* @actions/core
* @actions/exec
* [Nektos/act](https://github.com/nektos/act)
* [Docker](https://docs.docker.com/engine/install/)
* brew, scoop or curl


## Packages installation

### Mac

```bash
npm install --save-dev @actions/core
npm install --save-dev @actions/exec
brew install act
```

### Windows

```bash
npm install --save-dev @actions/core
npm install --save-dev @actions/exec
curl -s https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash
echo "OR"
scoop install act
```

## Executing

### Executing all workflows within a folder

***Note:*** The `--container-architecture linux/amd64` is used for the Macs with the new chipset. Not a required parameter, but will be used as part of the examples.

```bash
act --container-archictecture linux/amd64
```

### Graph the existing workflows

```bash
act --container-archictecture linux/amd64 -g
```

```bash
 ~ [main] act --container-architecture linux/amd64 -g 
 ╭────────────────╮ ╭────────────────────╮ ╭───────────────────╮ ╭────────────────────╮ ╭───────────────────────╮ ╭─────────────────────────╮ ╭────────────────────────────╮ ╭──────────────────────╮
 │ sandbox-deploy │ │ skuid-sync-actions │ │ production-deploy │ │ skuid-sync-actions │ │ sandbox-run-lint-tool │ │ sandbox-pre-check-merge │ │ production-pre-check-merge │ │ production-lint-tool │
 ╰────────────────╯ ╰────────────────────╯ ╰───────────────────╯ ╰────────────────────╯ ╰───────────────────────╯ ╰─────────────────────────╯ ╰────────────────────────────╯ ╰──────────────────────╯
 ~ [main] 
```

Using `-l` will produce a list with similar results

```bash
Stage  Job ID                      Job name                    Workflow name                     Workflow file                Events      
0      skuid-sync-actions          skuid-sync-actions          itd-sf-sandbox-deployment-script  deploy-sandbox.yml           push        
0      sandbox-deploy              sandbox-deploy              itd-sf-sandbox-deployment-script  deploy-sandbox.yml           push        
0      skuid-sync-actions          skuid-sync-actions          itd-sf-deployment-script          deploy.yml                   push,release
0      production-deploy           production-deploy           itd-sf-deployment-script          deploy.yml                   push,release
0      sandbox-run-lint-tool       sandbox-run-lint-tool       itd-sf-sandbox-pre-merge-check    pre-merge-check-sandbox.yml  pull_request
0      sandbox-pre-check-merge     sandbox-pre-check-merge     itd-sf-sandbox-pre-merge-check    pre-merge-check-sandbox.yml  pull_request
0      production-lint-tool        production-lint-tool        itd-sf-pre-merge-check            pre-merge-check.yml          pull_request
0      production-pre-check-merge  production-pre-check-merge  itd-sf-pre-merge-check            pre-merge-check.yml          pull_request

Detected multiple jobs with the same job name, use `-W` to specify the path to the specific workflow.
```

### Dry-run 

#### Dry-run on all workflows
```bash
act --container-archictecture linux/amd64 -n
```

#### Dry-run on a specific workflow. 

First execute the list to get the job id.

```bash
act --container-architecture linux/amd64 -l
```

Execute the command with the desired job id

```bash
act --container-architecture linux/amd64 -j  production-pre-check-merge -n
```

```bash
...
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Main Installing SF Git Delta Plugin
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge] ⭐ Run Main Installing Java
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Main Installing Java
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge] ⭐ Run Main Environment Login
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Main Environment Login
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge] ⭐ Run Main Checkout source code
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Main Checkout source code
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge] ⭐ Run Main Creating Delta packages for new, modified or deleted metadata
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Main Creating Delta packages for new, modified or deleted metadata
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge] ⭐ Run Main Environment Package(s) Deployment
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Main Environment Package(s) Deployment
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Main Salesforce Production Pre-deployment Dry-Run Job
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge] ⭐ Run Post Salesforce Production Pre-deployment Dry-Run Job
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Post Salesforce Production Pre-deployment Dry-Run Job
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge] Cleaning up container for job production-pre-check-merge
*DRYRUN* [itd-sf-pre-merge-check/production-pre-check-merge] 🏁  Job succeeded
Error: Could not find any stages to run. View the valid jobs with `act --list`. Use `act --help` to find how to filter by Job ID/Workflow/Event Name
```

### Normal Execution

Similar to the dry-run but without the `-n`

```bash
...
| dev 2.1.3
| devops-center 1.2.5
| env 3.0.13
| functions 1.22.11
| signups 2.0.13
| @salesforce/sfdx-plugin-lwc-test 1.1.1
| @salesforce/sfdx-scanner 3.19.0
[itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Main Installing SF Git Delta Plugin
[itd-sf-pre-merge-check/production-pre-check-merge] ⭐ Run Main Installing Java
[itd-sf-pre-merge-check/production-pre-check-merge]   🐳  docker exec cmd=[bash --noprofile --norc -e -o pipefail /var/run/act/workflow/1-composite-3.sh] user= workdir=
| /var/run/act/workflow/1-composite-3.sh: line 2: sudo: command not found
[itd-sf-pre-merge-check/production-pre-check-merge]   ❌  Failure - Main Installing Java
[itd-sf-pre-merge-check/production-pre-check-merge] exitcode '127': command not found, please refer to https://github.com/nektos/act/issues/107 for more information
[itd-sf-pre-merge-check/production-pre-check-merge]   ❌  Failure - Main Salesforce Production Pre-deployment Dry-Run Job
[itd-sf-pre-merge-check/production-pre-check-merge] exitcode '127': command not found, please refer to https://github.com/nektos/act/issues/107 for more information
[itd-sf-pre-merge-check/production-pre-check-merge] ⭐ Run Post Salesforce Production Pre-deployment Dry-Run Job
[itd-sf-pre-merge-check/production-pre-check-merge]   🐳  docker cp src=/Users/cristiano/.cache/act/csilva-intakedesk-salesforce-deployment@v2.0.5/ dst=/var/run/act/actions/csilva-intakedesk-salesforce-deployment@v2.0.5/
[itd-sf-pre-merge-check/production-pre-check-merge]   ✅  Success - Post Salesforce Production Pre-deployment Dry-Run Job
[itd-sf-pre-merge-check/production-pre-check-merge] 🏁  Job failed
Error: Job 'production-pre-check-merge' failed
```