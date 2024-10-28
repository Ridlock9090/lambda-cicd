-**Any Changes on S3 Config, a GitHub Actions Workflow is Initialized**

This GitHub Actions workflow, named "Validate CloudFormation on PR," is designed to validate and deploy AWS CloudFormation templates in response to pull requests (PRs) that affect files in the `cloudformation/` directory.

### Key Components:

- **Triggers:** 
  - The workflow runs on pull request events (opened, reopened, closed, or synchronized) specifically for paths under `cloudformation/**`.

- **Permissions:** 
  - It grants write access to pull requests and read access to repository contents.

### Jobs:

1. **validate-cfn:**
   - **Environment:** Runs on the latest Ubuntu.
   - **Conditions:** Executes only if the PR is not merged.
   - **Steps:**
     - Checks out the repository code.
     - Configures AWS credentials using secrets.
     - Validates a CloudFormation template located at `cloudformation/s3-bucket.yml`.
     - Deploys a CloudFormation stack named based on the PR number.
     - Comments on the PR to inform that the stack has been deployed.

2. **cleanup-on-merge:**
   - **Environment:** Also runs on the latest Ubuntu.
   - **Conditions:** Executes only when the PR is closed and merged.
   - **Steps:**
     - Configures AWS credentials.
     - Deletes the previously created CloudFormation stack associated with the PR.

### Summary:
The workflow automates the validation and deployment of CloudFormation templates for PRs, and it cleans up the resources once the PR is merged.
