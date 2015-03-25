# デバッグ

<!--original
# Debugging
-->

## デプロイの失敗を調査する

<!--original
## Investigating a failing deployment
-->

もし、デプロイで問題が発生して、基本設定の出力が機能しなかったとき、いくつかのとるべき方策があります。まず一番最初に、`--verbose`フラグをつけてコマンド実行を試しましょう。何が起きているのか、もっとたくさんの情報が表示されるはずです。

<!--original
If you encounter problems with your deployment and the default output isn't helping you, there is a handful of things you can do. First of all, try running whatever command you are running with the `--verbose` flag. This will display a lot more informations on what is happening.
-->

追加される主な情報は、どのコマンドがサーバで実行されたかと、サーバ上でリアルタイムにまさに何が起きているか、の2つです。

<!--original
The two main informations that are added, are what commands are run on the server, and what exactly is happening on the server, in real time:
-->

```bash
$ rocketeer deploy --verbose
```

```
|-- Running: Deploy (Deploys the website) [~14.25s]
|---- Running: Primer (Run local checks to ensure deploy can proceed)
|---- Running: CreateRelease (Creates a new release on the server) [~5.98s]
|------ Running strategy for Deploy: Sync
$ mkdir /home/www/myapp/releases/20140817160812
$ rsync ./ user@host:/home/www/myapp/releases/20140817160812 --verbose --recursive --rsh="ssh" --exclude=".git" --exclude="vendor"
[local] building file list ... done
[local] .gitattributes
[local] [...]
[local] sent 970690 bytes  received 1910 bytes  84573.91 bytes/sec
[local] total size is 964547  speedup is 0.99
|---- Running: Dependencies (Installs or update the dependencies on server) [~5.15s]
|------ Running strategy for Dependencies: Composer
$ cd /home/www/myapp/releases/20140817160812
$ /usr/local/bin/composer install --no-interaction --no-dev --prefer-dist
[user@host] (production) Loading composer repositories with package information
[user@host] (production) Installing dependencies from lock file
[user@host] (production) [...]
[user@host] (production)
[user@host] (production) Generating autoload files
[user@host] (production) Generating optimized class loader
[user@host] (production) Compiling common classes
[user@host] (production) Compiling views
|---- Running: Migrate (Migrates and/or seed the database) [~0.21s]
|------ Running strategy for Migrate: Artisan
$ rm -rf /home/www/myapp/current
$ ln -s /home/www/myapp/releases/20140817160812 /home/www/myapp/current
Successfully deployed release 20140817160812
|-- Running: Cleanup (Clean up old releases from the server) [~2.68s]
$ rm -rf /home/www/myapp/releases/20140817135635
Removing 1 release from the server
Execution time: 27.4618s
```

## 時間のかかるデプロイの調査

<!--original
## Investigating a slow deployment
-->

もし、あなたのデプロイが異常に遅い場合、それぞれのタスク名の横の括弧内に表示される時間をチェックすることを薦めます。

<!--original
If your deployment seems to be unusually slow, I recommend you check the times displayed in brackets displayed next to each task name:
-->

```
|---- Running: CreateRelease (Creates a new release on the server) [~5.98s]
```

この時間はタスク実行時間の平均です。もし、2回デプロイして、それに10秒、8秒とかかった場合、`[~8s]`と表示されます。この時間は _概算_ で、実際にタスクにかかる時間を示しているわけではなく、前回の結果に基づく予測です。

<!--original
This time is an average of the recorded execution times of this task. If you deployed twice and it took 10s and 8s, it'll display `[~8s]`. This time is an _estimation_ and does not represent the actual time the task will time, it's just a prediction based on previous observations.
-->

### 見つからないバイナリ、パーミッション問題ほかの調査

<!--original
### Investigating missing binaries, permission problems, etc.
-->

もし、特定のコマンドの実行をRocketeerが失敗することがわかった場合、まず一番に、 **そのコマンドを同じ資格情報(ユーザアカウント)で実行できるか確認します** 。Rocketeerは単に、あなたがサーバに指示したのと同じことをしているだけです。もし、あなたのサーバ上でコマンドの実行を妨げる問題があったら、(Rocketeerでも)同じ問題が起きます。

<!--original
If you find yourself in a situation where Rocketeer fails to execute a certain command, first of all, **check that with the same credentials you are able to run that command yourself**. Rocketeer only does what you tell him to, if there is a problem on your server that is preventing a command from working, it will encounter the same problem.
-->

ただし、Rocketeerと手動実行では、違いがひとつあります。Rocketeerはデフォルトで`shell-less`セッションで実行しようとします。つまり、BashやZshでログインするのではないので、`.bashrc`や`.zshrc`を読み込むような特定のツール(RVMやNVMなど)で問題になるかもしれません。

<!--original
There is however one main difference between Rocketeer and you: Rocketeer will by default run into a shell-less session. That means it doesn't login with Bash or Zsh, which is problematic for certain tools (like RVM, NVM, etc.) that are loaded via your `.bashrc` or `.zshrc` file.
-->

それらの問題に対処するため、Rocketeerには特定のコマンドをシェルから実行するためのオプションがあります。`remote.php`ファイルの中で、設定できます。

<!--original
To work around the problem, Rocketeer has an option to run certain commands in a shell, you can configure it in the `remote.php` file:
-->

```php
// Execution
//////////////////////////////////////////////////////////////////////

// If enabled will force a shell to be created
// which is requried for some tools like RVM or NVM
'shell'          => false,

// An array of commands to run under shell
'shelled'        => ['npm ', 'bundle ', 'grunt'],
```

シンプルに`shell`オプションを有効(true)にして、(シェル)ログインが必要なコマンドを配列に追加します。このオプションは、コマンドごとにマッチするので、実行コマンドの一部のみで有効にすることができます。

<!--original
Simply enable the `shell` option and add whatever command you need to be logged in for in the array. Those are matched against the command, meaning it can match only part of the executed command.-->
