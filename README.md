# Dockerfile tips

## Dockerfile Best Practices

This goes over the best pratices in the
[Docker Documentation](http://docs.docker.com/articles/dockerfile_best-practices/).

From [DockerCon EU 2014 video](https://www.youtube.com/watch?v=xNfCEie5_RA)
and [Slides](http://www.slideshare.net/rafecolton/dockercon-eu-2014).

 * Always read the Dockerfile from a base you're suing if you didn't
   built it yourself.

 * Set the Environment early in the file.

 * The files/directories that change less frequently should use the
   `ADD` command as early as possible in the Dockerfile. Since the
   system is built by layers the sooner it appears the better it is,
   since it will be in a layer that can be cached.

 * Specifically do `ADD .` as late as possible.

 * The `RUN` commands should be combined as much as possible, using
   the shell `&`. Each invocation of the `RUN` command creates an
   additional layer in the docker image. So the more invocations, the
   more layers, hence the bigger the image. It also forces the logic
   on the dependency of the actions.

 * Always use a standard entry point. Example:

 ```dockerfile
    ADD .docker/docker-entrypoint /docker-entrypoint

    VOLUME ["/app/style-galerry/public"]
    ENTRYPOINT ["/opcker-entrypoint"]
    CMD ["style-gallery"]
```

   the code that runs could be something like:

```bash
    run_entrypoint() {
        cd /path/to/app
        ## Operate on Docker specific environment variables.
        EXPORT SOME_ENV_VAR=<something>

        sed -i 's/patt1/new_patt1/g' <file>

        case "$1" in ...
        ...
    }

   run_entrypoint "$@"
```

  Another example from the official Postgres image:

```bash
    #!/bin/bash
    set -e

    if [ "$1" = 'postgres' ]; then
       chown -R postgres "$PGDATA"

       if [ -z "$(ls -A "$PGDATA")" ]; then
          gosu postgres initdb
       fi
       exec gosu postgres "$@"
   fi

   exec "$@"
```
