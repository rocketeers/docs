# Deploying

## Basic deployment

Once your credentials are properly setup after the [Getting Started](http://localhost/rocketeers/website/public/#/docs/I-Introduction/Getting-started) section, it's time to deploy your project.

If it's your first time deploying on the server, call the `rocketeer deploy` command, it'll setup your server and deploy for the first time. Depending on what strategy you picked, it will either clone or rsync your project on the server.
You can choose to only deploy to specific servers or stages by using the relevant flags:

```bash
$ rocketeer deploy
$ rocketeer deploy --on="staging,production"
$ rocketeer deploy --on="production" --stage="staging"
```

Once a first release is properly setup, to speed things up you can decide to use the **Update** task, which will not create a new release, but simply update the current one:

```bash
$ rocketeer update
```

The same options can be used as above. Per example if you use the **Clone** strategy, it will do a `git pull` on the current release, and re-run any relevant event listeners you created.

## Parallel deployments

By default Rocketeer executes everything synchronously, but via the `--parallel` flag you can decide to deploy everything in parallel. An important note on what this means: it will **not** run tasks within a queue in parallel, the order of tasks in a queue is crucial and thus that order is kept. What it will run in parallel, is the various connections and/or stages.

Say you have the following task registered:

```php
Rocketeer::task('some-task', ['foo', 'bar']);
```

If you have 3 connections, each with 2 servers and 2 stages, Rocketeer will spawn 12 processes in parallel, each doing sequentially `foo` then `bar` on the matching connections/stages.
