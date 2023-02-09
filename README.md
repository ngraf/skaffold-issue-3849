# skaffold-issue-3849 "Images not found in remote registry"
Reproducible code for Skaffold issue https://github.com/GoogleContainerTools/skaffold/issues/3849

Given you followed the following steps
----

1) Clone this repository

2) Set name of your Docker Registry as environment variable, e.g. your free https://hub.docker.com/ account
 

    $ export REPO=grafnverivox

3) Build version `v1` with Skaffold

   
    $ skaffold build --default-repo=${REPO} --tag v1

4) Build and push version `v2` with slighty different content with `docker`. (We do this via `docker buid` and not `skaffold build` to prevent `skaffold` gets to know what is going on. In reality the `v2` has been built and pushed on a different machine.)


    $ docker build -t ${REPO}/skaffold-issue-3849-demo:v2
    
5) Push the image just built with `docker`

    
    $ docker push ${REPO}/skaffold-issue-3849-demo:v2

6) Build version `v2` with Skaffold
    

    $ skaffold build --default-repo=${REPO} --tag v2

Then the current unexpected state is Skaffold builds it unnecessarily
----
```
Generating tags...
 - $REPO/skaffold-issue-3849-demo -> $REPO/skaffold-issue-3849-demo:v2
Checking cache...
 - 🟠 $REPO/skaffold-issue-3849-demo: Not found. Building
```

Then my expected state is that Skaffold detects the image is already in remote registry and pulls it
----
```
Generating tags...
 - $REPO/skaffold-issue-3849-demo -> $REPO/skaffold-issue-3849-demo:v2
Checking cache...
 - 🟢 $REPO/skaffold-issue-3849-demo: Found remotely
```

