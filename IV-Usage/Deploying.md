# デプロイ

<!--original
# Deploying
-->

## 基本的なデプロイ

<!--original
## Basic deployment
-->

一度、資格情報が正しく[セットアップ](../I-Introduction/Getting-started.md)されたら、いよいよあなたのプロジェクトをデプロイするときです。

<!--original
Once your credentials are properly setup after the [Getting Started](#/docs/docs/I-Introduction/Getting-started) section, it's time to deploy your project.
-->

もし、これがサーバへの最初のデプロイであれば、`rocketeer deploy`コマンドを呼びましょう。初回時のためのサーバセットアップとデプロイを実行してくれます。あなたのサーバ上のプロジェクトは、どのストラテジーを取っているかによって、クローンまたはrsyncされます。
次のようなフラグを使うことで、特定のサーバやステージに限定してデプロイすることが可能です。

<!--original
If it's your first time deploying on the server, call the `rocketeer deploy` command, it'll setup your server and deploy for the first time. Depending on what strategy you picked, it will either clone or rsync your project on the server.
You can choose to only deploy to specific servers or stages by using the relevant flags:
-->

```bash
$ rocketeer deploy
$ rocketeer deploy --on="staging,production"
$ rocketeer deploy --on="production" --stage="staging"
```

一度、最初のリリースが正常にセットアップされれば、スピードアップのためにタスクの **更新(update)** を選択できるようになります。これは、新しいリリースを作らず単純に今のものを更新します。

<!--original
Once a first release is properly setup, to speed things up you can decide to use the **Update** task, which will not create a new release, but simply update the current one:
-->

```bash
$ rocketeer update
```

上記(のdeploy)と同じオプションを使うことができます。例として、もし **クローン** ストラテジーをとっていれば、現在のリリースに対して`git pull`を実行し、あなたが作成した関連のイベントリスナを再実行します。

<!--original
The same options can be used as above. Per example if you use the **Clone** strategy, it will do a `git pull` on the current release, and re-run any relevant event listeners you created.
-->

## 試験実行(ドライラン)

<!--original
## Dry runs
-->

[translation here]

<!--original
If you're unsure about your deployment procedure, you can use the `--pretend` flag to display what commands and tasks would be run were you to execute the command normally.
-->

```bash
$ rocketeer deploy --pretend
```

```
|-- Running: Deploy (Deploys the website) [~14.25s]
|---- Running: Primer (Run local checks to ensure deploy can proceed)
|---- Running: CreateRelease (Creates a new release on the server) [~5.98s]
|------ Running strategy for Deploy: Sync
$ mkdir /home/www/myapp/releases/20140817160632
$ rsync ./ host@server:/home/www/myapp/releases/20140817160632 --verbose --recursive --rsh="ssh" --exclude=".git" --exclude="vendor"
|---- Running: Dependencies (Installs or update the dependencies on server) [~5.15s]
|------ Running strategy for Dependencies: Composer
$ cd /home/www/myapp/releases/20140817160632
$ /usr/local/bin/composer install --no-interaction --no-dev --prefer-dist
|---- Running: Migrate (Migrates and/or seed the database) [~0.21s]
|------ Running strategy for Migrate: Artisan
$ mv /home/www/myapp/current /home/www/myapp/releases/20140817160632
$ rm -rf /home/www/myapp/current
$ ln -s /home/www/myapp/releases/20140817160632 /home/www/myapp/current
Successfully deployed release 20140817160632
|-- Running: Cleanup (Clean up old releases from the server) [~2.68s]
$ rm -rf /home/www/myapp/releases/20140817135635
Removing 1 release from the server
Execution time: 3.5601s
```

## 並行デプロイ

<!--original
## Parallel deployments
-->

[translation here]

<!--original
By default Rocketeer executes everything synchronously, but via the `--parallel` flag you can decide to deploy everything in parallel. An important note on what this means: it will **not** run tasks within a queue in parallel, the order of tasks in a queue is crucial and thus that order is kept. What it will run in parallel, is the various connections and/or stages.
-->

[translation here]

<!--original
Say you have the following task registered:
-->

```php
Rocketeer::task('some-task', ['foo', 'bar']);
```

[translation here]

<!--original
If you have 3 connections, each with 2 servers and 2 stages, Rocketeer will spawn 12 processes in parallel, each doing sequentially `foo` then `bar` on the matching connections/stages.
-->
