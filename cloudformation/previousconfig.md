name: Validate CloudFormation on PR

on:
 pull_request:
   paths:
     - 'cloudformation/**'
   types: [closed] 

permissions:
 pull-requests: write
 contents: read

jobs:
 validate-cfn:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-2

    - name: Validate Cloudformation template
      run: |
        aws cloudformation validate-template --template-body file://cloudformation/s3-bucket.yml

    - name: Deploy our stack
      run: |
        stack_name="ridly-pr-test-stack-${{ github.event.pull_request.number }}"
        aws cloudformation create-stack --stack-name $stack_name --template-body file://cloudformation/s3-bucket.yml --parameters ParameterKey=Environment,ParameterValue=test

    - name: Comment on the PR
      uses: actions/github-script@v7
      with:
            github-token: ${{secrets.GITHUB_TOKEN}}
            script: |
                github.rest.issues.createComment({
                 issue_number: context.issue.number,
                 owner: context.repo.owner,
                 repo: context.repo.repo,
                 body: 'Cloudformation test stack deployed. Stack name: ridly-pr-test-stack-${{ github.event.pull_request.number }}'
                })

                
 cleanup-on-merge:
    runs-on: ubuntu-latest
    if: github.event.pull_request.merged == true
    steps:
    - name: configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-2
            
    - name: Delete Test stack
      run: |
            stack_name="ridly-pr-test-stack-${{ github.event.pull_request.number }}"
            aws cloudformation delete-stack --stack-name $stack_name
          