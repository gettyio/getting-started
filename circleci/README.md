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

#### Platform

to set up your repository on CircleCI is pretty straightforward, with the right permission (the creator, normally the one who sets up have admin rights on the repo) go to the left-menu _Add Projects_ and click on the right placed button and click on _Set Up Project_ and then on Step 2, on _Start Building_, that's it, your pipeline will start.

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

#### Dockerizing

Running code in a container is a very common feature and your node application can be easily dockerized with a commited Dockerfile, in the root of the repository would be our case:
```
# Dockerfile
FROM node:10

USER node

WORKDIR /home/node/repo

COPY --chown=node:node package.json ./
COPY --chown=node:node node_modules/ ./node_modules
COPY --chown=node:node build/ ./build

CMD yarn start
```

and with building and testing taken cared of, we can only care about using previously generated build and dependencies from cache and build our image:

```
  ...
  docker-build-push:
    docker:
      - image: docker:17.05.0-ce-git
    steps:
      - checkout
      - setup_remote_docker

      # build and push docker image
      - run: |
          export OWNER_NAME="gettyio"
          export PROJECT_NAME="myproject"
          docker login -u $DOCKER_LOGIN -p $DOCKER_PWD
          docker build . --tag $OWNER_NAME/$PROJECT_NAME:$CIRCLE_SHA1
          docker push $OWNER_NAME/$PROJECT_NAME:$CIRCLE_SHA1

workflows:
...
```

the whole code is available at [common/docker.yml](common/docker.yml)
