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