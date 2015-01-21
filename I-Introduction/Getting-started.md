# セットアップ
> # Setup

プロジェクトにRocketeerをインストールするのはとても簡単です。インストール方法はいくつかあります。
> Installing Rocketeer on a project is quite easy, there are several ways to do so.

## アーカイブからインストールする
> ## Via the compiled archive

最も簡単な方法は、アーカイブをダウンロード[ダウンロードアドレス](http://rocketeer.autopergamene.eu/versions/rocketeer.phar)して、アプリケーションのディレクトリに入れる方法です。
> The easiest way is to just get the archive by doing downloading it [at the following addresss](http://rocketeer.autopergamene.eu/versions/rocketeer.phar) and putting it in your application's directory.

どこのフォルダからも rocketeer コマンドが使えるようにするには、以下のコマンドでグローバル環境にインストールします。
> You can also install it globally by executing the following command which will then give you a global `rocketeer` command to use in any folder.

```bash
$ wget http://rocketeer.autopergamene.eu/versions/rocketeer.phar
$ chmod +x rocketeer.phar
$ mv rocketeer.phar /usr/local/bin/rocketeer
```

これで`rocketeer ignite`と打ち込むだけで、プロジェクトをセットアップするための質問が開始されます。あとはうまくいくはずです。
> Then you just need to type `rocketeer ignite`. You'll get asked a series of question to setup your project and you should be good to go.

## Composerでインストールする
> ## With Composer

### ローカル環境で
> ### Locally

他のパッケージと同じように、Composerを使ってインストールできます。
> You can also install Rocketeer with Composer as any package :

```bash
$ composer require anahkiasen/rocketeer
$ php vendor/bin/rocketeer ignite
```

たったこれだけです。`yourapp/.rocketeer/`に、下記で参照する設定ファイルが作成されます。
> And that's all, the configuration file referenced below will be created at `yourapp/.rocketeer/`.

Rocketeerのコマンドにアクセスするには、`php vendor/bin/rocketeer`としてください。`alias rocketeer=php vendor/bin/rocketeer`とシンプルにエイリアスすることもできます。もしくは、`vendro/bin/`を_PATH_に加えることで、ローカルのバイナリに常時アクセスできるようにできます。
> Use `php vendor/bin/rocketeer` to access Rocketeer's commands or simply alias it : `alias rocketeer=php vendor/bin/rocketeer`. You can also add `vendor/bin` to your _PATH_ to always have local binaries accessible.

### Globally

As with all Composer packages, Rocketeer can also be installed as a global dependency by preprending `global` :

```bash
$ composer global require anahkiasen/rocketeer
$ rocketeer ignite
```

## Configuration

You'll do most of your configuration in Rocketeer's configuration files, in the `.rocketeer` folder.
There are a lot of options in the configuration file, each is explained thoroughly so take your time to read the comments carefully.

## Using Rocketeer

If you're using the archive, you'll need to hit `php rocketeer.phar` to access the commands, per example `php rocketeer.phar deploy` or if you installed it globally, `rocketeer deploy`.

Once you're done with the configuration, it is recommended to run the `php rocketeer.phar check` command, it will run various commands on the server to check whether the latter is ready to receive your application.

Then you can just hit `php rocketeer.phar deploy`. It will create an initial release on your remote server.
Afterwards, to update it you can either run the same command again which will create an entirely new release, or simply do an `php rocketeer.phar update` which will update the repository and dependencies of your application.

### Folder organization

Rocketeer recognizes its custom configuration in the folder you currently are in. Same thing for the custom `tasks.php` and `events.php` files, so you could have that organization :

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
