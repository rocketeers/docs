# Laravel との統合

<!--original
# Laravel integration
-->

もともとRocketeerはLaravelのパッケージで、そのコンポーネントのいくつかを使っていたために、フレームワークとの深い連携を提供しています。以下はLaravelと一緒にセットアップする方法です。

<!--original
Because it was originally a Laravel package and uses some of its components, Rocketeer provides deep integration with the framework. Here is how to setup the package with it.
-->

## インストール

<!--original
## Installation
-->

以下のコマンドで、Rocketeerを開発依存パッケージの一つとして加えます。

<!--original
Start by adding Rocketeer as one of your dev-dependencies with the following command:
-->

```bash
$ composer require --dev anahkiasen/rocketeer
```

終了したら、アプリケーションの`app/config/local/app.php`ファイル内に、Rocketeerのプロバイダを追加する必要があります。

<!--original
Once this is done, you'll need to add Rocketeer's provider to your application's in `app/config/local/app.php` file:
-->

```php
'providers' => append_config(array(
	// ...
	'Rocketeer\RocketeerServiceProvider',
)),
```

そして、以下が`app/config/local/app.php`の`aliases`配列に追加する行です。

<!--original
Then, this line to the `aliases` array in your `app/config/local/app.php` file:
-->

```php
'aliases' => append_config(array(
	// ...
	'Rocketeer' => 'Rocketeer\Facades\Rocketeer',
)),
```

確実に、開発依存パッケージとしてRocketeerが読み込まれた後に、[`append_config()`][0]ヘルパーを使った'ローカルの'環境設定によるこれらの変更が行われるようにします。

<!--original
Ensuring that these changes are made in your 'local' environment configuration using the [`append_config()`][0] helper function since Rocketeer has been included as a dev-dependency.
-->
 
## プロジェクトのセットアップ

<!--original
## Setting up the project
-->

アプリケーションにRocketeerを追加したら、`artisan list`と打つとすべての`deploy:`名前空間に紐づけされたデプロイコマンドが見れるはずです。これは、`rocketeer update`などの例ごとに、代わりに`artisan deploy:update`と実行することを意味しています。
しかしながら、簡単に`artisan deploy`としてデプロイタスクを実行することができるようなエイリアスを追加もできます。

<!--original
Now that you added Rocketeer to the application, if you hit `artisan list` you should see all of the deployments commands bound to the `deploy:` namespace. That means instead of doing `rocketeer update` per example you will do `artisan deploy:update`.
There is however an alias added for convenience which allows you to do `artisan deploy` and run the Deploy task.
-->

さあ`artisan deploy`コマンドを打ちましょう。これによって、Rocketeerの設定が、`app/config/packages/anahkiasan/rocketeer`に作成されます。その中にはいつものファイル群があるはずです。

<!--original
Now hit the `artisan deploy:ignite` command. This will publish Rocketeer's configuration to `app/config/packages/anahkiasen/rocketeer`, in there you will find all the usual files.
-->

## 追記

<!--original
## Additions
-->

これまでのことが行われたので、安全にタスク、ストラテジーまたはバイナリーをアプリケーションの名前空間に定義することができ、Rocketeerによって認識されます。`app/config/remote.php`に定義されているコネクションも認識され、自身に定義されているコネクションにマージされます。

<!--original
Now that this is done you can safely define tasks, strategies or binaries in your application's namespace and they will be recognised by Rocketeer. It will also recognise connections defined in `app/config/remote.php` and merge them with any connection defined in its own configuration.
-->

単にアプリケーションのproviders配列に追加することで、他の追加のプラグインを登録することもできます。プラグインの設定ファイルは、`artisan config:publish vendor/package` を実行することで作成することができます。

<!--original
You can also register any additional plugin simply by adding its provider to the application's providers array. The plugin's configuration can also be publish by doing the usual `artisan config:publish vendor/package`
-->

[0]: http://laravel.com/docs/4.2/configuration#provider-configuration

<!--original
[0]: http://laravel.com/docs/4.2/configuration#provider-configuration
-->
