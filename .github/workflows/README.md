# .github/workflows/cfn-validate-pr.yml

## Any Changes on S3 Config, a GitHub Actions Workflow is Initialized

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

---

# .github/workflows/cleanup-on-merge.yml is a disabled workflow after incorporating all the code into one workflow in cfn-validate-pr.yml

This GitHub Actions workflow, titled "Clean up on merge," is designed to automatically clean up AWS CloudFormation stacks associated with pull requests that have been merged and closed.

### Key Components:

- **Triggers:** 
  - The workflow activates on `pull_request` events, specifically when a pull request is closed and affects files in the `cloudformation/**` directory.

### Permissions:
- Grants write access to pull requests and read access to repository contents.

### Job:

1. **cleanup-on-merge:**
   - **Environment:** Runs on the latest Ubuntu.
   - **Conditions:** Executes only if the pull request is closed and has been merged. This is checked using the condition: `github.event.action == 'closed' && github.event.pull_request.merged == true`.
   - **Steps:**
     - **Configure AWS Credentials:** Uses the `aws-actions/configure-aws-credentials` action to set up AWS credentials, allowing the workflow to interact with AWS resources. It uses secrets to safely handle the AWS access key ID and secret access key.
     - **Delete Test Stack:** Runs a command to delete the CloudFormation stack associated with the merged pull request. The stack name is dynamically generated based on the pull request number.

### Summary:
The workflow automates the cleanup of AWS resources by deleting the CloudFormation stack linked to a merged pull request, ensuring that test stacks do not persist unnecessarily after their purpose has been served.

---

# .github/workflows/lambda.yml

## Changes to the Lambda code, runs a GitHub Actions workflow to update AWS Lambda code

This GitHub Actions workflow, titled "Deploy AWS Lambda," automates the deployment of AWS Lambda functions when changes are pushed to the `main` branch, specifically for files in the `LAMBDA/` directory.

### Key Components:

- **Triggers:** 
  - The workflow runs on `push` events to the `main` branch, targeting changes in the `LAMBDA/**` path.

### Jobs:

1. **deploy-lambda:**
   - **Environment:** Runs on the latest Ubuntu.
   - **Steps:**
     - **Checkout Code:** Uses the `actions/checkout` action to retrieve the repository's code.
     - **Set Up Python:** Installs Python version 3.12 using the `actions/setup-python` action.
     - **Install Dependencies:** Upgrades pip and installs any necessary dependencies specified in `LAMBDA/requirements.txt`, placing them in a `lambda/` directory.
     - **Configure AWS Credentials:** Sets up AWS credentials using secrets for access to AWS resources.
     - **Deploy Lambda Function:** Zips the contents of the `LAMBDA` directory and updates the specified Lambda function (`my-test-cicd-lambda`) with the new code.

### Summary:
This workflow facilitates the automated deployment of an AWS Lambda function by checking out code, setting up the Python environment, installing dependencies, configuring AWS credentials, and deploying the updated function upon pushes to the main branch.

