# Concourse Exercise 1 - Setup Concourse

For this workshop we will be using a shared Concourse instance. Each participant should
have been assigned a user account, and can login to Concourse at:

https://concourse.workshop.paasify.org

Once logged in, download and install the `fly` CLI using the links provided in the browser.

## Log In to Fly

Almost all interaction with Concourse is done via the Fly CLI, so lets login:

```
fly -t workshop login \ 
    -c https://concourse.workshop.paasify.org \ 
    -u <username> -p <password>
```

To ensure that you've logged in correctly lets issue the command to retrieve pipelines:

```fly -t workshop pipelines```
