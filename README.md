## GETTY IO DEVOPS

#

#### Share to Care

Sharing is the right tool to allow others to absorb and share thoughts that will help to avoid the error others already faced or even get better chances to have a better coding with a few less or more lines or even chars of code.

#### Automating integrations and delivery (CI/CD)

A good way to not go crazy when the services grow on management is to automate repetitive tasks a operator would do manually, via terminal or a plataform. And again, a way to share knowledge bases for coders and operators fellas. Even better if it has an skeleton project to proove a concept of a deployment pipeline. Eg.: with AWS Amplify.

### CI/CD:

- [CircleCI](#circleci)

### Tech Stack

#### Languages used in Dev and Ops:
- NodeJS
- Go
- Shell/Bash Script

#### Databases:

- MongoDB

#### Secrets:

- Our secrets are shared by [Keybase](https://keybase.io/docs)
, an multiple-featured encryption tool based on GPG.

#### Infrastructure:
- CloudFormation (Infrastructure As Code for custom implementations)
- EC2
- EKS
- Lambda
- CloudFront

#### CircleCI

##### Getting Started

1. [Local: How to setup circleci local cli](circleci#local-circleci-cli)

2. [First Job: Develop your first job and test it locally](circleci#first-job)

3. [Workflows: How your pipeline will behave](circleci#workflow)

4. [Platform: How to setup a repo in CircleCI](circleci#platform)

5. [Caching: How to share data between jobs](circleci#caching)

6. [Docker: How to dockerize your application](circleci#dockerizing)

7. [External Dependencies: How emulate a external dependency service](circleci#external-dependencies)

##### AWS

1. Serverless

TODO: how to build a reusable serverless (framework) deployment pipeline for CircleCI.

2. Amplify

TODO: how to build a reusable amplify (framework) deployment pipeline for CircleCI.

3. CloudFormation

TODO: how to build a reusable cloudformation (aws-cli) deployment pipeline for CircleCI.

##### Dockerizing

TODO: how to construct a Dockerfile that is populated by already built packages and binaries.
