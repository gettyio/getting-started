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
- building
- linting and testing
- deploying

Workflows can have multiple features enabled to best attend your needs, from basic to more complex cases:

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
# jobs will normally be up here

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

the build and test use-case code is available at [common/workflow.yml](common/workflow.yml)

#### Caching

Before anything else, the why of caching when using workflows is that data is not persistent through the jobs of workflows, we will use our next step's structure as an example:
```
# build job will be up here in the yaml
  test:
    docker:
      - image: circleci/node:10

    steps:
      # lints and test code
      - run: |
          yarn run lint
          yarn run test

workflows:
  version: 2
  build-test:
    jobs:
      - build
      - test:
          requires:
            - build
```

a first thought is that this will do to have a working workflow with build and testing for your code delivering to be neat, but acctually, there are two missing things for us to succeed, checking out code, and caching depencies, because without them there is no code or tools to test for, so:
```
      ...
      # tries to save dependencies folder to cache
      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }}
      
      # build distribution folder
      - run: yarn build

  test:
    docker:
      - image: circleci/node:10

    steps:
      - checkout

      # tries to restore dependencies folder from cache
      - restore_cache:
          keys:
          - v1-dependencies-{{ checksum "package.json" }}
      ...
```

as data is not persistent, caching will make this bridge between `build` when necessary, like this, you don't need to download dependencies or rebuild distributions all over again. As for the code, it could be cached also, but for small repositories, checking out again will do just fine.

the whole code is available at [common/caching.yml](common/caching.yml)