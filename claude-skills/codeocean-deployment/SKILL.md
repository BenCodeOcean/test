---
name: codeocean-deployment
description: Deploy, update, delete, and manage Code Ocean environments in AWS accounts using the covpc CLI tool. Use when user mentions deploying, creating, updating, or managing Code Ocean environments, deployments, or stacks. Also use this skill when working on new features that need testing in a Code Ocean environment.
context: fork
agent: general-purpose
allowed-tools:
  - Bash
  - Read
  - Grep
  - Glob
  - WebFetch
  - Task
  - TodoWrite
  - AskUserQuestion
---

# Code Ocean Deployment Skill

This skill manages Code Ocean deployments in AWS accounts using the `covpc` CLI tool.

**IMPORTANT**: When using the Task tool to spawn subagents in this skill, always set `model: "haiku"` for cost and performance optimization.

## 1. Use Cases

- **Creating new deployments** for testing features or development
- **Updating existing deployments** after code changes
- **Deleting deployments** when no longer needed
- **Listing all deployments** to see what environments exist
- **Feature development** - deploy and test changes in a real Code Ocean environment

## 2. Prerequisites

### AWS SSO Authentication

Commands require AWS SSO. If authentication is needed, the browser opens automatically. Inform the user when this happens.

## 3. Rules & Policies

### CircleCI Build Check (Before create/update)

Before `create-deployment` or `update-deployment`, check CircleCI build status:

```bash
# Get latest pipeline for branch
curl -s -H "Circle-Token: $CIRCLE_TOKEN" \
  "https://circleci.com/api/v2/project/gh/OceanCodes/infra-private-cloud/pipeline?branch=<branch_name>" \
  | jq '.items[0]'

# Get workflow status for pipeline
curl -s -H "Circle-Token: $CIRCLE_TOKEN" \
  "https://circleci.com/api/v2/pipeline/<pipeline_id>/workflow" \
  | jq '.items[] | {name, status}'
```

- **Running**: Wait and check periodically
- **Failed**: Stop and notify user immediately
- **Successful** or no recent builds: Proceed

### User Approval Requirements

| Command | Approval Required |
|---------|-------------------|
| `create-deployment` | Yes - show full command first |
| `update-deployment` | Yes (first time only); subsequent updates during feature dev: No |
| `delete-deployment` | Always (destructive!) + ask if user wants to wait |
| `show-deployments` | No (read-only) |
| `wait-for-deployment` | No (monitoring) |
| `check-deployment-availability` | No (monitoring) |
| `init-deployment` | No (standard workflow step) |

### Stack Naming Convention

1. **User provides name**: Use it directly
2. **Branch is `main`**: Ask for custom name (never use "main" as stack name)
3. **Feature branch**: Suggest short derived name (e.g., `change-ecs-image-pull-behavior-sc-154872` → `ecs-image-pull`)
4. **Always confirm** with user before proceeding

### Deployment URL Format

```
https://<stack-name>.aws.codeocean.dev/
```

Always print this URL when deployment is ready.

## 4. Commands Reference

### create-deployment

Creates a new Code Ocean deployment.

```bash
covpc create-deployment [options]
```

| Flag | Description | Default |
|------|-------------|---------|
| `--account ACCOUNT` | AWS account (e.g., "dev", "acmecorp", account ID) | dev |
| `--deploy-role ROLE` | IAM role for deployment | CodeOceanLeastPrivilegedDeployRole |
| `--role ROLE` | IAM role to use (Administrator, PowerUser) | Administrator |
| `--stack STACK` | Stack name | current branch name |
| `--ref REF` | Infra repo ref (branch, tag) for template | current branch |
| `--region REGION` | Deployment region (e.g., us-east-1, us-east-2) | None |
| `--assumable-roles ROLES` | Additional assumable roles | [] |
| `--slim / --no-slim` | Create slim (dev) deployment | True |
| `--owner OWNER` | Owner name (added as stack tag) | None |
| `--multi-az / --no-multi-az` | Enable multiple AZs | False |
| `--param KEY=VALUE` | Custom CloudFormation parameter (repeatable) | None |

### update-deployment

Updates an existing deployment. Accepts all `create-deployment` options plus:

| Flag | Description | Default |
|------|-------------|---------|
| `--maintenance / --no-maintenance` | Keep maintenance mode during update | False |
| `--keep-tags / --no-keep-tags` | Keep existing tags during update | False |

### delete-deployment

Deletes a deployment. Uses same base options as create (--account, --stack, --region, --role).

### init-deployment

Initializes a newly created deployment (triggers CircleCI cypress workflow). Requires `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, and `CIRCLE_TOKEN`.

### wait-for-deployment

Waits for deployment operation to complete.

| Flag | Description | Default |
|------|-------------|---------|
| `--interval INTERVAL` | Time between status checks (seconds) | 60 |
| `--pipeline PIPELINE` | CI pipeline ID of the deployment operation | None |

### check-deployment-availability

Checks if deployment is available and responding.

| Flag | Description | Default |
|------|-------------|---------|
| `--interval INTERVAL` | Time between checks (seconds) | 15 |
| `--timeout TIMEOUT` | Minutes before timeout | 45 |

### show-deployments

Lists all Code Ocean deployments in an account. Returns detailed information about each deployment including status, owner, region, reference, type (slim/full), creation date, cost, and deployment URL.

**IMPORTANT**: This command should **always be run in a sub agent** using the Task tool with `subagent_type=general-purpose` and `model="haiku"`.

Use this command when the user asks about:
- Listing deployments
- Checking what environments exist
- Deployment details (status, cost, owner, etc.)
- Specific deployment information

| Flag | Description | Default |
|------|-------------|---------|
| `--account ACCOUNT` | AWS account | dev |
| `--role ROLE` | IAM role to use | Administrator |

## 5. Workflows

### New Deployment

1. Determine stack name (see naming convention)
2. Ask about slim, region, custom params
3. Check CircleCI status
4. Show command → get approval
5. `create-deployment`
6. `wait-for-deployment`
7. `init-deployment`
8. `check-deployment-availability`
9. Print deployment URL

### Update Deployment

1. Check CircleCI status
2. Show command → get approval (first time only)
3. `update-deployment`
4. **Always** run `wait-for-deployment` (not optional)
5. **Always** run `check-deployment-availability` (not optional)
6. Print deployment URL

### Delete Deployment

1. Show command → get approval (always required)
2. Ask: "Wait for deletion to complete, or return immediately?"
3. `delete-deployment`
4. If waiting: run `wait-for-deployment` in background
5. Report completion

### Feature Development

1. Create deployment (if not exists)
2. Make code changes
3. Wait for CircleCI build
4. Update deployment (no approval for iterations)
5. Test changes
6. Repeat 2-5 as needed
7. Delete deployment when done (with approval)

## 6. Examples

**"Deploy a new environment called my-feature"**
1. Ask about slim, region, custom params
2. Check CircleCI
3. Show command and get approval
4. Run: `covpc create-deployment --stack my-feature --account dev --slim --region us-east-1`
5. Wait → init → check → print URL

**"Update the deployment"**
1. Determine stack name
2. Check CircleCI
3. Show command and get approval (first time only)
4. Run: `covpc update-deployment --stack <name> --account dev --region us-east-1`
5. Wait → check → print URL

**"Delete the test-env deployment"**
1. Show command and get approval + ask about waiting
2. Run: `covpc delete-deployment --stack test-env --account dev --region us-east-1`
3. If waiting: run wait-for-deployment

**"Show me all deployments"**
1. Run: `covpc show-deployments --account dev`
2. Display results (no approval needed)

## 7. Operations

### Error Handling

| Error | Action |
|-------|--------|
| AWS SSO expired | Browser opens for re-auth |
| CircleCI build failed | Alert user, ask how to proceed |
| Deployment failed | Show error details, ask for next steps |

### Background Tasks

This skill runs in a forked context. Use `run_in_background: true` for long-running operations.

Use descriptive names in Bash `description` parameter:
- `"Waiting for <stack-name> deployment to complete"`
- `"Waiting for <stack-name> stack deletion to complete"`
- `"Checking <stack-name> availability"`
