# 接続とステージ

<!--original
# Connections and stages
-->

サーバーとの通信に関して、二つの主要なコンセプトに出会うでしょう。それは**接続**と、**ステージ**です。

<!--original
You'll meet two main concepts when it comes to communicating with your server : **Connections** and **Stages**.
-->

## 接続

<!--original
## Connections
-->

### 設定

<!--original
### Configuration
-->

接続は、接続したいいろいろなのサーバーを示します。これらは、`config.php`の中に設定しましょう。接続の設定は以下のような感じになります。

<!--original
Connections represent the various servers you may want to connect to. You'll configure those in `config.php`. Here's what a connection may look like :
-->

```php
'connections' => array(
  'production' => array(
    'host'      => 'my-server.com',
    'username'  => 'johndoe',
    'password'  => '',
    'key'       => '/Users/johndoe/.ssh/id_rsa',
    'keyphrase' => '',
  ),
),
```

二つの方法でサーバーに接続できます。パスワードを使うか、SSHキーを使うかです。ですので、もし接続にパスワードを使う場合、keyやkeyphraseは、空のままにするなどしておけます。

<!--original
You can connect by two ways to a server : via a password, or via an SSH key. So if you're connection with a password you can leave `key` and `keyphrase` empty, etc.
-->

重要な注意：機密情報をここに入れる必要は**ありません**。ロケッティアは、バージョントラックされるファイルの中で機密情報を空のままにしておけるように、フォールバックの仕組みを使います。もしこれらのすべてのフィールドを空にしておくと、ロケッティアはそれらの入力をうながし隠しファイルに保存するようにします。ただ重要なことは、コネクションが定義されていることです。
以下のようにです。

<!--original
Important note : you do **not** _have_ to put your credentials here. Rocketeer uses a fallback system that allows you to not have to put your credentials in files that may be tracked. So if you leave all of those fields empty, Rocketeer will prompt you for them and store them in an hidden file. All that matters is that your connection is _defined_. Meaning this :
-->

```php
'connections' => array(
  'production' => array(),
),
```

これで、接続として完璧に機能します。

<!--original
Is a perfectly valid connection.
-->

### 複数接続

<!--original
### Multiple connections
-->

これはとても基本的な接続でした。複数接続が必要といった、もっと複雑な設定について考えます。

<!--original
Now this was a fairly basic connection. Now imagine a more complicated setup, you might have multiple connections :
-->

```php
'connections' => array(
  'production' => array(
    'host' => 'my-server.com',
  ),
  'staging' => array(
    'host' => 'staging.my-server.com',
  ),
),
```

さて、これらの二つの接続を動作させるのに、いくつかの方法があります。一つは、私たちが編集してきた`config.php`のdefault arrayの値を変更することです。

<!--original
From there to interact with those two connections, there are multiple ways. First you can change the value of the `default` array in the `config.php` file we've been editing :
-->

```php
'default' => array('production', 'staging'),
```

このarrayの意味するのは、あなたがタスクを実行するときはいつでも、Rocketeerはこれらすべての接続に対して実行するということです。しかし、これはあなたの望むものではないかもしれません。何かしらのチェリーピックを何かに対して行いたいなどですね。そのためには`--on`フラグを使うことができます。

<!--original
What this array means is : whenever you'll execute a Task, Rocketeer will execute it on all of those connections. But now, that may not be what you want, you may want to cherry-pick what you do on what. For this you'll use the `--on` flag.
-->

デプロイコマンドですと、通常次のように実行します。`rocketeer deploy` 。いずれかの接続を選んでデプロイするには、一つまたはいくつかの接続を以下のようにフラグを付けて渡します。

<!--original
Take a deploy command, you'd execute it like this normally : `rocketeer deploy`. To select which connections to deploy to, you'll pass one or more connections to it as a flag :
-->

```bash
$ rocketeer deploy --on="staging"
$ rocketeer deploy --on="staging,production"
```

複数サーバー接続

<!--original
### Multiserver connections
-->

時には、実際には複数のサーバーになる接続があって、接続を扱う時にそれらが個々に作用するようにしたいと思うでしょう。Rocketeerでは接続を定義する際に一つのシンプルな`servers`　arrayを使うことでそれを可能にしています。

<!--original
Sometimes you might have a connection that is actually represented by multiple servers, and want each of those affected when dealing with that connection. Rocketeer allows that using a simple `servers` array when defining your connection:
-->

```php
'connections' => array(
  'production' => array(
    'servers' => array(
      array(
        'host'      => 'first-server.com',
        'username'  => 'johndoe',
        'password'  => '',
        'key'       => '/Users/johndoe/.ssh/id_rsa',
        'keyphrase' => '',
      ),
      array(
        'host'      => 'second-server.com',
        'username'  => 'johndoe',
        'password'  => '',
        'key'       => '/Users/johndoe/.ssh/id_rsa',
        'keyphrase' => '',
      ),
    ),
  ),
),
```

## ステージ

<!--original
## Stages
-->

Rocketeerの世界では、**与えられた接続**に対してステージごとに分離した環境になります。これはRocketeerによってひな形として作成された典型的なフォルダ構成です。

<!--original
In the realm of Rocketeer, stages are separated environments on **a given connection**. Here is a classic folder organization scaffolded by Rocketeer :
-->

```
| my-app
| -- current
| -- releases
| ----- 20140119010101
| -- shared
```

一方、一つのサーバーで`testing`と`production`の複数ステージだとこのようになります。

<!--original
A server with multiple stages, say `testing` and `production` will on the other hand look like this :
-->

```
| my-app
| -- testing
| ----- current
| ----- releases
| -------- 20140119010101
| ----- shared
| -- production
| ----- current
| ----- releases
| -------- 20140519413121
| ----- shared
```

ステージに対して行うことはすべてステージの中で完結します。

<!--original
Everything you do on a stage is self-contained.
-->

### 設定

<!--original
### Configuration
-->

ステージの設定は非常に簡単です。あなたは`stages`ファイルを開きたいと思うでしょう。ステージの数とその名前を定義するには、単にarrayに書き入れます。（設定の例ごとに上に示しています）

<!--original
Configuring stages is fairly easy, you'll want to open the `stages` file. To define how many stages you have and their names, simply fill the array (per example for our setup above) :
-->

```php
'stages' => array('testing', 'production'),
```

接続と同じように、デフォルトでタスクを実行したときに、どのステージで実行されるか`default`arrayで選ぶことができます。全く同じ方法です。

<!--original
Just like for connections, you can chose which stages to execute tasks on by default via the `default` array. It works exactly the same way :
-->

```php
'default' => array('testing'),
```

### 複数ステージ

<!--original
### Multiple stages
-->

接続と同じように、特定のステージでタスクを動作させるようにフラグを渡すことができます。

<!--original
Just like for connections, you can pass a flag to execute tasks on a particular stage :
-->

```bash
$ rocketeer test --stage="testing"
```

## 状況によって変化するの設定

<!--original
## Contextual configuration
-->

一つの設定ファイルの組み合わせだけであればすべて問題なくよいのですが、もし3つのサーバーがあってそれぞれ3つのステージがある場合はもちろん設定は一つ一つ異なります。

<!--original
Now that's all good and stuff but, you only have one set of configuration files, if you have three servers each with three stages, surely the configuration will vary from one to the other.
-->

Rocketeerは状況によって変化する設定を、メインの`config.php`(接続を定義したファイルです)の中のネストされたarrayによって扱います。それぞれのPHPのパスが異なる2つのサーバーという簡単で典型的な例をとりあげます。通常PHPのパスは`paths.php`ファイルの`php`配下にあります。ですので、2つの接続について以下のように定義します。

<!--original
Rocketeer handles contextual configuration via nested arrays, which you can find in the main `config.php` file (where you define your connections). Let's take a fairly classic example, you have two servers, and the path to PHP is different in both. Now usually the path to PHP can be found in the `paths.php` file, under `php`. So here's how we'd define it for our two connections :
-->

```php
'on' => array(
  'connections' => array(

    'staging' => array(
      'paths' => ['php' => 'PATH-TO-PHP'],
    ),
    'production' => array(
      'paths' => ['php' => 'PATH-TO-PHP'],
    ),

  ),
),
```

`stagin`、`production`いずれかに入れたいすべての設定が、Rocketeerが作成した設定ファイルのミニチュアとしてここに入れられます。上書きするには、単にファイルと同じ名前のarray（ここでは`paths`）を作成し、設定をその中に記述します。

<!--original
Everything you'll put in either `staging` or `production` here will be a miniature version of the configuration files Rocketeer created. To override any key, simply create an array named like the file (here `paths`) and put the config in there.
-->

### ファイルベースで行う

<!--original
### File-based alternative
-->

多くの上書きしたい設定を進めるときに、すごく長くなり、ネストされたarrayの中のネストされたarrayの中のネストされたarrayの中で設定したくないでしょう。Rocketeerは、デフォルトで設定フォルダの中にある、すべての`connections`と`stages`フォルダを読みに行きます。`production`接続上で`master`ブランチを、`staging`接続上で`develop`ブランチをデプロイしたいとすれば、以下のようにファイルを作成します。

<!--original
Now as this can get quite lengthy and you don't want to work in an array nested in an array nested in an array nested in an array, here's how you can proceed if you have a lot of things to override. Rocketeer will by default read any `connections` and `stages` folder created within the configuration folder. Say you want to deploy the `master` branch on your `production` connection, and deploy the `develop` branch on your `staging` connection, you'd create the following files:
-->

```
| .rocketeer
| -- connections
| ---- production
| ------ scm.php
| ---- staging
| ------ scm.php
```

**connections/productions/scm.php** ファイル

<!--original
**connections/production/scm.php**
```php
<?php return array(
  'branch' => 'master',
);
```
-->

**connection/staging/scm.php** ファイル

<!--original
**connections/staging/scm.php**
```php
<?php return array(
  'branch' => 'develop',
);
```
-->

ブランチについても同じように、`.rocketeer/stages/staging/somefile.php`のように作成して、デフォルトの設定の各オプションを上書きします。

<!--original
Same goes for branches, just create per example `.rocketeer/stages/staging/somefile.php` to override some options from the default configuration.
-->
