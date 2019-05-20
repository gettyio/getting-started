# CircleCI

#### Local CircleCI cli

Quick installation:

`$ curl -fLSs https://circle.ci/cli | bash`

##### Usage

Check if configuation file is valid:

`$ circleci config validate`

Local job invoking:

`$ circleci local execute --branch mybranch --job myjob`

#### First Job

As a first job task, only dependencies downloading and distribution folder building will be done, simple as that.

Snippet, whole code is available at [common/job.yml](common/job.yml):
```
# downloads dependencies
- run: |
    yarn
    yarn global add typescript

# builds distribuion folder
- run: yarn build
```

as i named it `build` and my current branch is `development`, the local execution will be something like this:

`$ circleci local execute --branch development --job build`

#### Workflow

Workflows will help you organize your jobs into flows to attend your needs, we've tried to list some of our known cases like:
- linting and testing
  - auxiliary services
    - mongodb
    - sqs
- deploying
  - backend (EKS)
  - frontend (CloudFront)

Workflows can have multiple features enabled to best attend your needs, from basic:

- Build and Testing flow, to guarantee your code is buildable and passes tests requirements:
```
workflows:
  version: 2
  build-test:
    jobs:
      - build
      - test:
          # test will only be executed if build succeeds
          requires:
            - build
```
- Build and Deployment with branch-specific behavior:
```
# jobs will be up here

workflows:
  version: 2
  build-test:
    jobs:
      - build

      # this job will only be trigged when branch development gets a commit pushed
      - deploy-development:
          requires:
            - build
          filters:
            branches:
              only: development

      # this job will only be trigged when branch master gets a commit pushed
      - deploy-prod:
          requires:
            - build
          filters:
            branches:
              only: master
```

the basic use-case code is available at [common/workflow.yml](common/workflow.yml)