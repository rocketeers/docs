# ストラタジー

<!--original
# Strategies
-->

## イントロダクション

<!--original
## Introduction
-->

Rocketeerはデプロイする人が想像するだろう基本タスクは、あらかじめ全て備えています——新しいリリースの作成、そのテスト、データベースのマイグレーション、など。ほとんどのタスクは一通りのやり方しかありませんが、いくつかの基本タスクには、目的を達成するのにいくつかの方法が存在しています。それがストラタジーです。

<!--original
Rocketeer has all the core tasks you'd expect from a deployer: creating a new release, testing it, migrating the database, etc. Most tasks have only one way to do things, but certain core ones have multiple paths they can use to accomplish their goals: those are the strategies.
-->

ストラタジーを利用する一番重要なタスクは、デプロイと更新です。デフォルトでは、あなたのSCM(Git, Svnなど)を使ってクリーンな新しいリリースをサーバからクローンしますが、それがストラタジーとして利用可能な全てではではありません。`rocketeer strategies`コマンドで、どのタスクがストラタジーを利用しているか、どのオプションが利用可能かを確認することができます。コマンドは、次のような表を出力します。

<!--original
The most important tasks using strategies are the Deploy and Update ones, by default they use your configured SCM (Git, Svn, etc.) to clone a clean new release on the server, but that's not the only strategy available. You can see what tasks use strategies and what options are available by calling the `rocketeer strategies` command. It'll yield a table similar to the following:
-->

| ストラタジー | 実装 | 説明 |
| -------------- | ---------------- | ----------------------------------------------------------------------- |
| check          | Php              | サーバがPHPアプリケーションを受け取り可能か確認 |
| check          | Ruby             | サーバがRubyアプリケーションを受け取り可能か確認Checks |
| deploy         | Clone            | SCMでリポジトリの新しいインスタンスをクローン |
| deploy         | Copy             | リポジトリのクローン済みインスタンスを複製して、アップデート |
| deploy         | Sync             | ローカルファイルからrsyncを使って作成または更新 |
| test           | Phpunit          | PHPUnitでテストを実行 |
| migrate        | Artisan          | データベースをLaravelのArtisan CLIでマイグレート |
| dependencies   | Composer         | Composerで依存パッケージをインストール |
| dependencies   | Bundler          | Bundlerで依存パッケージをインストール |
| dependencies   | Npm              | NPMで依存パッケージをインストール |
| dependencies   | Bower            | Bowerで依存パッケージをインストール |
| dependencies   | Polyglot         | 上記のパッケージマネージャを必要に応じて実行 |

<!--original
| Strategy       | Implementation   | Description                                                             |
| -------------- | ---------------- | ----------------------------------------------------------------------- |
| check          | Php              | Checks if the server is ready to receive a PHP application              |
| check          | Ruby             | Checks if the server is ready to receive a Ruby application             |
| deploy         | Clone            | Clones a fresh instance of the repository by SCM                        |
| deploy         | Copy             | Copies the previously cloned instance of the repository and update it   |
| deploy         | Sync             | Uses rsync to create or update a release from the local files           |
| test           | Phpunit          | Run the tests with PHPUnit                                              |
| migrate        | Artisan          | Migrates your database with Laravel's Artisan CLI                       |
| dependencies   | Composer         | Installs dependencies with Composer                                     |
| dependencies   | Bundler          | Installs dependencies with Bundler                                      |
| dependencies   | Npm              | Installs dependencies with NPM                                          |
| dependencies   | Bower            | Installs dependencies with Bower                                        |
| dependencies   | Polyglot         | Runs all of the above package managers if necessary                     |
-->

どのタスクがどのストラタジーを使用するかは、設定フォルダ内の`strategies.php`ファイルで設定することが可能です。

<!--original
You can configure which task uses which strategy in the `strategies.php` file of your configuration folder.
-->

## 独自のストラタジーを追加する

<!--original
## Adding your own strategies
-->

Rocketeerには、それぞれのストラタジーに利用可能ないくつかの実装がありますが、組み込まれていない特定のツールや手順を使いたいこともあります。独自のストラタジーを追加することは非常に簡単で、そこには本当に2つのステップしかありません。例として、Nodeアプリケーションを使うケースで、PHPunitのかわりにGruntを使いたいとしましょう。

<!--original
Rocketeer comes with a handful of available implementations for each strategy but sometimes you use a particular tool or procedure that isn't built-in. It's really easy to add your own strategies, there's only two steps really. Say you want to test your application with Grunt instead of PHPunit, in the case of a Node application per example.
-->

最初に、`Rocketeer\Abstracts\AbstractStrategy`を拡張してクラスを作成します。もし、RocketeerがComposerの依存モジュールとしてプロジェクトにロードされているなら、クラスは自動ロードされる限りどきにども置けます。そうでなければ、`.rocketeer/strategies`以下に作成する必要があります:

<!--original
First you'll create a class for your strategy extending `Rocketeer\Abstracts\AbstractStrategy`. If Rocketeer is loaded as a Composer dependency of your project, the class can be anywhere as long as it's autoloaded, otherwise you need to create it under `.rocketeer/strategies`:
-->

```php
<?php
namespace Acme;

use Rocketeer\Abstracts\Strategies\AbstractStrategy;

class GruntStrategy extends AbstractStrategy
{
  // ...
}
```

次に、どのストラタジー該当するのか定義する必要があります。そのために、関係するインターフェースを`Rocketeer\Interfaces\Strategies`名前空間から実装するでしょう。私たちの場合であれば、`TestStrategyInterface`は次のようになります:

<!--original
Next we need to define which strategy this corresponds to, for this you'll implement the relevant interface from the `Rocketeer\Interfaces\Strategies` namespace, in our case, `TestStrategyInterface`:
-->


```php
<?php
namespace Acme;

use Rocketeer\Abstracts\Strategies\AbstractStrategy;
use Rocketeer\Interfaces\Strategies\TestStrategyInterface;

class GruntStrategy extends AbstractStrategy implements TestStrategyInterface
{
  public function test()
  {
    // ...
  }
}
```

あと残るのは、ビジネスロジックを書くことだけです。ストラタジーはタスクというより、同じ特徴(トレイト)を実装するので、通常のすべてのツール、特にバイナリを使うことができます。(より詳しい情報は、関連するドキュメントのセクションを見てください)

<!--original
All that is left to do is write the business logic. Strategies implements the same traits than Tasks so you have all the usual tools available, in particular binaries (see the relevant documentation section for more informations).
-->


```php
<?php
namespace Acme;

use Rocketeer\Abstracts\Strategies\AbstractStrategy;
use Rocketeer\Strategies\Interfaces\TestStrategyInterface;

class GruntStrategy extends AbstractStrategy implements TestStrategyInterface
{
  public function test()
  {
    return $this->binary('grunt')->runForCurrentRelease('test');
  }
}
```

これは、設定された接続において、`grunt test`を実行します。残る作業は、Rocketeerをそのストラタジーを使うように設定することです。`strategies.php`ファイルを次のように書きます:

<!--original
This will run `grunt test` on the configured connection. All that is left to do is to configure Rocketeer to use that strategy, in the `strategies.php` file:
-->

```php
// Which strategy to use to test your application
'test'         => 'Acme\Grunt',
```

もう一度、作ったクラスがどこかにロードされているか確認しましょう。もし、`.rocketeer/strategies`にあるならRocketeerは自分自身でロードするでしょう。もし、Rocketeerがあなたのアプリケーションの依存モジュールになっているなら、クラスがComposerによってロードされているか確認しましょう。

<!--original
Again, make sure your class is loaded somewhere, if it's in `.rocketeer/strategies`, Rocketeer will load it by itself. If Rocketeer is a dependency of your application, make sure the class is loaded by Composer.-->
