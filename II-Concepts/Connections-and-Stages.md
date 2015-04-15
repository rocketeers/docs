# 接続とステージ

<!--original
# Connections and stages
-->

サーバーとの通信に関して、二つの主要なコンセプトに出会うでしょう。: **接続** と、**ステージ** です。

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

接続は、接続したいいろいろなのサーバーを示します。これらは、`config.php`の中に設定しましょう。接続の設定は以下のような感じになります。:

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

二つの方法でサーバーに接続できます。: パスワードを使うか、SSHキーを使うかです。

<!--original
You can connect by two ways to a server : via a password, or via an SSH key. So if you're connection with a password you can leave `key` and `keyphrase` empty, etc.
-->

[translation here]

<!--original
Important note : you do **not** _have_ to put your credentials here. Rocketeer uses a fallback system that allows you to not have to put your credentials in files that may be tracked. So if you leave all of those fields empty, Rocketeer will prompt you for them and store them in an hidden file. All that matters is that your connection is _defined_. Meaning this :
-->

```php
'connections' => array(
  'production' => array(),
),
```

[translation here]

<!--original
Is a perfectly valid connection.
-->

### 複数接続

<!--original
### Multiple connections
-->

[translation here]

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

[translation here]

<!--original
From there to interact with those two connections, there are multiple ways. First you can change the value of the `default` array in the `config.php` file we've been editing :
-->

```php
'default' => array('production', 'staging'),
```

[translation here]

<!--original
What this array means is : whenever you'll execute a Task, Rocketeer will execute it on all of those connections. But now, that may not be what you want, you may want to cherry-pick what you do on what. For this you'll use the `--on` flag.
-->

[translation here]

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

[translation here]

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

[translation here]

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

[translation here]

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

[translation here]

<!--original
Everything you do on a stage is self-contained.
-->

### 設定

<!--original
### Configuration
-->

[translation here]

<!--original
Configuring stages is fairly easy, you'll want to open the `stages` file. To define how many stages you have and their names, simply fill the array (per example for our setup above) :
-->

```php
'stages' => array('testing', 'production'),
```

[translation here]

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

[translation here]

<!--original
Just like for connections, you can pass a flag to execute tasks on a particular stage :
-->

```bash
$ rocketeer test --stage="testing"
```

## コンテキストの設定

<!--original
## Contextual configuration
-->

[translation here]

<!--original
Now that's all good and stuff but, you only have one set of configuration files, if you have three servers each with three stages, surely the configuration will vary from one to the other.
-->

[translation here]

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

[translation here]

<!--original
Everything you'll put in either `staging` or `production` here will be a miniature version of the configuration files Rocketeer created. To override any key, simply create an array named like the file (here `paths`) and put the config in there.
-->

### ファイルベースの

<!--original
### File-based alternative
-->

[translation here]

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

[translation here]

<!--original
**connections/production/scm.php**
```php
<?php return array(
  'branch' => 'master',
);
```
-->

[translation here]

<!--original
**connections/staging/scm.php**
```php
<?php return array(
  'branch' => 'develop',
);
```
-->

[translation here]

<!--original
Same goes for branches, just create per example `.rocketeer/stages/staging/somefile.php` to override some options from the default configuration.
-->
