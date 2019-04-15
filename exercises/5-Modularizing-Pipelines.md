# Concourse Exercise 5 - Modularizing Pipelines

. It is very common within an application's git repository to place all concourse artifacts in a 
`ci` folder.  Within that folder individual tasks are modularized in their own yml files and refer 
to shell scripts for actual task execution.  E.G.

```
App Git Repo Root:
├── App source code....
│   ├── Some code artifacts...
├── ci
│   ├── pipeline.yml
│   ├── credentials.yml
│   ├── tasks
│   │   ├── modular-task.yml
│   │   ├── modular-task.sh
```

We will mimic that structure as we build out our pipeline

Create the `ci` and `ci/tasks` directory structure:

```
mkdir -p ci/tasks
```

Move the `pipeline.yml` file into your new `ci` directory

```
mv pipeline.yml ci/pipeline.yml
```

We will now create modularized tasks for the steps to build our application.  First 
create the yml task file `mvn-test.yml`.  Create the file in the `ci/tasks` directory:

```
---
platform: linux

image_resource:
  type: docker-image
  source:
    repository: jamesdbloom/docker-java8-maven
    tag: "latest"

inputs:
- name: git-assets

run:
  path: git-assets/ci/tasks/test.sh
```

You'll note that this task references to script `test.sh` for the actual execution logic.  Create that file:

```
#!/bin/bash

set -xe

cd git-assets
mvn test
```

Create similar artifacts for the packaging stage of your application build:

_ci/tasks/mvn-package.yml_

```
---
platform: linux

image_resource:
  type: docker-image
  source:
    repository: jamesdbloom/docker-java8-maven
    tag: "latest"

inputs:
- name: git-assets

outputs:
- name: app-output

run:
  path: git-assets/ci/tasks/package.sh
```

_ci/tasks/package.sh_

```
#!/bin/bash

set -xe

cd git-assets
mvn package
cp target/concourse-demo-*.jar ../app-output/concourse-demo.jar
```

We need to make sure our bash scripts are executable.  Execute the following command:

```
$ chmod +x ci/tasks/*.sh
```

Modify your main `pipleine.yml`, which now resides in the the ci directory, to include the 2 new tasks.  
These tasks will represent completely new jobs in your pipeline.  These will test and package the Java code 
included in your repository.  You will be replacing your "howdy" job.  Your final pipeline.yml file should 
look like this:

```
resources:
- name: git-assets
  type: git
  source:
    branch: master
    uri: https://github.com/nthomson-pivotal/concourse-workshop

jobs:
- name: unit-test
  public: true
  plan:
  - get: git-assets
    trigger: true
  - task: mvn-test
    file: git-assets/ci/tasks/mvn-test.yml
- name: deploy
  public: true
  plan:
  - get: git-assets
    trigger: true
    passed:
      - unit-test
  - task: mvn-package
    file: git-assets/ci/tasks/mvn-package.yml
```

Update your concourse pipeline with the set-pipeline command.  Remember, your pipeline.yml file is now in a different location:

```
fly -t default set-pipeline -p pipeline -c ci/pipeline.yml
```

You'll note your pipeline now steps (or jobs).  1) Unit test your code and 2) Package/Deploy your code.  
Step #2 will only kickoff if Step #1 is successful.

We will now trigger the pipeline as a group at the end of the exercise.