# Concourse Exercise 2 - Tasks

Using a text editor, create the file `pipeline.yml`:

```
platform: linux
image_resource:
  type: docker-image
  source: {repository: ubuntu}
run:
  path: echo
  args: ["Hello, My first pipeline!"]
```

To execute the Concourse task on your local workstation execute the following fly command.  
Your environment may pull down a Docker image after which you will see the task initialize and execute.

```
fly -t workshop execute -c pipeline.yml
```