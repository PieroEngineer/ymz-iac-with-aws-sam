# What is Yummizi (ymz)?

Yummizi was a foodtech startup project that allowed restaurants to prepare diners' food while they were on their way to the establishment. Although I didn't continue with the project due to validation issues, it taught me a lot about marketing, sales, and full-stack development.

I think that the best way to be grateful for what I have is to share it. Here, I share with you what I learned by building a stable, real-time platform that connects diners with restaurants the Yummizi way.

# Sub-systems of the platform

+ [AWS Lambda with hono](https://github.com/PieroEngineer/ymz-aws-lambda-with-hono) (TS) 
+ [AWS serverless configuration with AWS SAM](https://github.com/PieroEngineer/ymz-iac-with-aws-sam) (yaml) 
+ [Client for process observability](https://github.com/PieroEngineer/ymz-admin-client-with-python) (Python) 
+ [Landing page](https://github.com/PieroEngineer/ymz-landing-page) (HTML + JS + CSS) 
+ [Restaurant Web app](https://github.com/PieroEngineer/ymz-restaurant-client-side) (TS + Vite + React) 
+ [Diner Android app](https://github.com/PieroEngineer/ymz-diner-client-side) (Kotlin)

# Yummizi Infrastructure

AWS infrastructure for the Yummizi backend, deployed with AWS SAM and AWS
CloudFormation. The root template orchestrates nested stacks for the API,
identity, compute, data, and media-delivery resources.

## Architecture

| Nested template | Resources |
| --- | --- |
| `nested/apigateway.yaml` | API Gateway HTTP API, stage, and Cognito JWT authorizer |
| `nested/cognito.yaml` | Cognito user pool, app client, and pre-sign-up Lambda trigger |
| `nested/dynamodb.yaml` | Provisioned DynamoDB single table with two global secondary indexes |
| `nested/lambda.yaml` | Hono backend Lambda, API routes, and scoped DynamoDB/S3 permissions |
| `nested/s3.yaml` | Private, encrypted, versioned media bucket with lifecycle rules |
| `nested/cloudfront.yaml` | CloudFront distribution using Origin Access Control to read from S3 |

The API uses a JWT authorizer for application routes. The HTTP API's CORS
configuration currently allows `https://yummizi.vercel.app` and
`http://localhost:5173`. The S3 bucket blocks public access; media is served
through CloudFront.

## Prerequisites

- AWS CLI configured with credentials for the target account and region.
- AWS SAM CLI.
- Node.js 24 and the backend project's dependencies for building the Lambda
  functions.
- Permissions to create the AWS resources in the templates, including
  CloudFormation, IAM, Lambda, API Gateway, Cognito, DynamoDB, S3, and
  CloudFront resources.

## Configure the backend source

The Lambda templates reference the backend project using absolute Windows paths
in `CodeUri`. Before building or deploying from another machine, update the
`CodeUri` values in `nested/lambda.yaml` and `nested/cognito.yaml` to the
location of the backend project. The paths must point to the project containing
the `package.json` and referenced source files.

## Build and deploy

From this directory, build the application and deploy it:

```powershell
sam build
sam deploy
```

The default SAM configuration in `samconfig.toml` deploys the stack named
`yummizi-backend` to `us-east-1`, with `Environment` set to `dev`. It resolves
the deployment artifact bucket automatically and prompts for confirmation of
the changeset. The template accepts `dev`, `staging`, or `prod` as the
environment:

```powershell
sam deploy --parameter-overrides Environment=staging
```

For a first deployment or to change saved deployment settings, use:

```powershell
sam deploy --guided
```

Review the CloudFormation changeset before approving a deployment. Several
resources currently have `DeletionPolicy: Delete`; review those policies before
using this stack for production data.

## Stack outputs

The root stack exports the DynamoDB table name and ARN, Cognito user pool and
client identifiers, S3 bucket details, CloudFront distribution details, and
API endpoint. The `YummiziFunctionArn` output is available from the stack but
is not exported for cross-stack import.

Retrieve outputs after deployment with:

```powershell
aws cloudformation describe-stacks `
  --stack-name yummizi-backend `
  --query "Stacks[0].Outputs"
```

## Configuration notes

- The API stage name and resource names are based on the selected environment.
- The DynamoDB table name is currently fixed as `YummiziTable`; deploying
  multiple environments into one AWS account may therefore require changing
  the table naming strategy.
- API CORS origins and the backend `CodeUri` locations are currently defined
  directly in the nested templates.
- The S3 bucket name includes the environment and AWS account ID to help ensure
  global uniqueness.


## Author

Made with 💙 by **Piero Olivas Tuesta**
