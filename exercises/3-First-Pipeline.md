# Concourse Exercise 3 - First Pipeline

Modify the file `pipeline.yml` that you previously created.  In this step we are creating a 
job within a pipeline, rather than running a one-off task:

```
jobs:
- name: hello
  plan:
  - task: howdy
    config:
      platform: linux
      image_resource:
        type: docker-image
        source: {repository: ubuntu}
      run:
        path: echo
        args: ["Hello, My first pipeline!"]
```

To deploy this pipeline to the concourse server use the `fly` command to set a pipeline:

```
fly -t workshop set-pipeline -p pipeline -c pipeline.yml
```

You'll note the command informs us that initially our pipeline is in a paused state.  We could 
unpause from the commandline, but instead we'll unpause and execute the pipeline from the Concourse 
web UI. 

In a browser navigate to https://concourse.workshop.paasify.org/, and click the Play button that you
will see in the pipeline entry. The state should change to 'pending'.

Now
- Open the pipeline by clicking on the name
- Click on the 'hello' job
- Press the '+' button to kick off the pipeline
- Track the build log of run #1

