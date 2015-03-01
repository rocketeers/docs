# セットアップ

<!--original
# Setup
-->

プロジェクトにRocketeerをインストールするのはとても簡単です。以下のいずれかの方法でインストールできます。

<!--original
Installing Rocketeer on a project is quite easy, there are several ways to do so.
-->

## コンパイル済みのアーカイブを使う方法

<!--original
## Via the compiled archive
-->

最も簡単な方法は、[以下のアドレス](http://rocketeer.autopergamene.eu/versions/rocketeer.phar)からアーカイブをダウンロードしてアプリケーションのフォルダに入れることです。

<!--original
The easiest way is to just get the archive by doing downloading it [at the following addresss](http://rocketeer.autopergamene.eu/versions/rocketeer.phar) and putting it in your application's directory.
-->

もしくは、以下のコマンドを実行して、グローバル環境にインストールすれば、グローバルなコマンドとしてどこのフォルダでも`rocketeer`コマンドを使うことができるようになります。

<!--original
You can also install it globally by executing the following command which will then give you a global `rocketeer` command to use in any folder.
-->

```bash
$ wget http://rocketeer.autopergamene.eu/versions/rocketeer.phar
$ chmod +x rocketeer.phar
$ mv rocketeer.phar /usr/local/bin/rocketeer
```

あとは、ただ、`rocketeer ignite`と打ち込むだけです。セットアップするために必要ないくつもの質問が聞かれます。あとはうまくいくはずです。

<!--original
Then you just need to type `rocketeer ignite`. You'll get asked a series of question to setup your project and you should be good to go.
-->

## Composerを使う方法

<!--original
## With Composer
-->

### ローカル環境へ

<!--original
### Locally
-->

Rocketeerは、Composerで他のパッケージと同じようにインストールできます。

<!--original
You can also install Rocketeer with Composer as any package :
-->

```bash
$ composer require anahkiasen/rocketeer
$ php vendor/bin/rocketeer ignite
```

これだけです。下で説明する設定ファイルが、`yourapp/.rocketeer/` の下に作成されます。

<!--original
And that's all, the configuration file referenced below will be created at `yourapp/.rocketeer/`.
-->

Rocketeerのコマンドにアクセスするには、`php vendor/bin/rocketeer`を使うか、これを`alias rocketeer=php vendor/bin/rocketeer`とエイリアスしておきます。ローカルのバイナリにいつでもアクセスできるように`vendor/bin`を_PATH_に追加しておくのでもよいでしょう。 

<!--original
Use `php vendor/bin/rocketeer` to access Rocketeer's commands or simply alias it : `alias rocketeer=php vendor/bin/rocketeer`. You can also add `vendor/bin` to your _PATH_ to always have local binaries accessible.
-->

### グローバル環境へ

<!--original
### Globally
-->

すべての Composer パッケージと同じように、`global`を付与することで、Rocketeerをグローバル環境へインストールすることもできます。

<!--original
As with all Composer packages, Rocketeer can also be installed as a global dependency by preprending `global` :
-->

```bash
$ composer global require anahkiasen/rocketeer
$ rocketeer ignite
```

## 設定

<!--original
## Configuration
-->

Roketeerのほとんどの設定は、`.rocketeer`フォルダの中の設定ファイルで行います。
設定ファイルには多くのオプションがあります。各オプションには説明が記載されていますのでコメントを注意して読んでください。

<!--original
You'll do most of your configuration in Rocketeer's configuration files, in the `.rocketeer` folder.
There are a lot of options in the configuration file, each is explained thoroughly so take your time to read the comments carefully.
-->

## Rocketeer を使う

<!--original
## Using Rocketeer
-->

もし、アーカイブを使っている場合には、コマンドにアクセスするために、`php rocketeer.phar`と打ち込む必要があります。例ごとに`php rocketeer.phar deploy`というようにです。グローバルにインストールしている場合には、`rocketeer deploy`と打てます。

<!--original
If you're using the archive, you'll need to hit `php rocketeer.phar` to access the commands, per example `php rocketeer.phar deploy` or if you installed it globally, `rocketeer deploy`.
-->

設定を終えたら、`php rocketeer.phar check`コマンドを実行することをおすすめします。これはサーバー上でいくつものコマンドを実行し、設定がアプリケーションを収容する準備が整っているかどうかをチェックします。

<!--original
Once you're done with the configuration, it is recommended to run the `php rocketeer.phar check` command, it will run various commands on the server to check whether the latter is ready to receive your application.
-->

続いて、`php rocketeer.phar deploy`と打ち込むと、最初のリリースがリモートサーバーに作成されます。
その後、新しいリリースを作成したら、同じコマンドを実行することで、アップデートができます。または単純にリポジトリと依存パッケージがアップデートされる`php rocketeer.phar update`を実行することでもアップデート可能です。

<!--original
Then you can just hit `php rocketeer.phar deploy`. It will create an initial release on your remote server.
Afterwards, to update it you can either run the same command again which will create an entirely new release, or simply do an `php rocketeer.phar update` which will update the repository and dependencies of your application.
-->

### フォルダ構成

<!--original
### Folder organization
-->

Rocketeer は、現在いるフォルダにある固有の設定ファイルを認識します。tasks.phpと、event.phpについても同様です。したがって以下のように構成するとよいでしょう。

<!--original
Rocketeer recognizes its custom configuration in the folder you currently are in. Same thing for the custom `tasks.php` and `events.php` files, so you could have that organization :
-->

```
| .rocketeer
| -- config.php
| -- hooks.php
| -- paths.php
| -- remote.php
| -- scm.php
| -- stages.php
| -- events.php
| -- tasks.php
| rocketeer.phar
| index.php
| public
| etc.
```

