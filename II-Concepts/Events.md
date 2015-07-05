# イベント

<!--original
# Events
-->

Rocketeerは、タスクによってドライブされるその中心にいます。各々のタスクは、それらの起動中に発火するインベントのそのすべての体系をもっています。（イベントは[illuminate/events](https://github.com/illuminate/events)コンポーネントによって動作しています。）
したがって、以下のようにした場合に、

<!--original
Rocketeer is at its core driven by Tasks. Each of these tasks has a whole ecosystem of events fired in their lifetime, powered by the [illuminate/events](https://github.com/illuminate/events) components. Therefor, when you're doing this :
-->

```php
Rocketeer::before('deploy', 'MyApp\MyCustomTask');
```

実際に行われることは、シンプルには以下になります。

<!--original
What you're actually doing is, more simply put :
-->

```php
$app['events']->listen('rocketeer.deploy.before', 'MyApp\MyCustomTask@execute');
```

もちろん"これだけ"ではありません。Rocketeerは、2つ目のパラメータで若干の魔法をしています。`Rocketeer::before('deploy', 'composer install')`とすれば`composer install`を、イベントディスパッチャが呼び出せる実際のタスククラスへと変化させます。しかしコンセプトにより、これは単なる基本の _Dispacher_（または好みによっては_Observer_）システムです。

<!--original
This is not "just this" of course, as Rocketeer does some magic on the second argument so that `Rocketeer::before('deploy', 'composer install')` transforms `composer install` into an actual Task class the Events Dispatcher can call. But in its concept, it's just your basic _Dispatcher_ (or _Observer_ if you like) system.
-->

## イベントの登録

<!--original
## Registering events
-->

まず最初に、イベントは、Composerによって自動的に読み込まれるすべてのファイルに登録できます。もしRocketeerのアーカイブを使っていて、更に特別な自動読み込みをしていないなら、Rocketeerは、デフォルトで`.rocketeer/events.php`を読み込もうとします。もし、多くのイベントがあって、複数のファイルに分けたい場合には、`.rocketeer/events/*`にファイルを設置すれば自動的に読み込まれます。

<!--original
First of all, you can register events in any file that is autoloaded by Composer. If you're using the Rocketeer archive and have no particular autoloading, Rocketeer will by default try to load `.rocketeer/events.php` or if you have more events and want to split them in multiple files, it'll autoload every file in a `.rocketeer/events/*` folder.
-->

## イベントへリストする

<!--original
## Listening to events
-->

Rocketeerのすべてのタスクは2つの基本的なイベント―――フックできる`before`と`after`―――を発生します。しかし、いくつかのタスクはその実行中に、様々なポイントでアクションの実行ができる内部イベントを発生します。それらのイベントをリッスンするには、2つのメソッドが使えます。今**デプロイ**タスクが`carrent`フォルダを最新リリースにシンボリックリンクする前に、何かを実行したいとします。

<!--original
All tasks in Rocketeer fire two basic events : `before` and `after` on which you can hook. But some tasks fire internal events, during their execution, allowing you to execute actions at various points in their lifetime. To listen to these events, there are two methods you can use. Say you want to execute something before the **Deploy** task symlinks the `current` folder to the latest release :
-->

```php
Rocketeer::addTaskListeners('deploy', 'before-symlink', function ($task) {
  echo $task->releasesManager->getCurrentRelease();
});

// Or

Rocketeer::listenTo('deploy.before-symlink', function ($task) {
  echo $task->releasesManager->getCurrentRelease();
});
```

これら2つのメソッドは見た目は似通っていますが、1つ目は実際2つ目を呼び出しますが、タスク名またはタスクのインスタンスを配列で渡すことを許可します。

<!--original
These two methods look very similar but in appearance only, the first one actually calls the second but allows you to pass an array of tasks names or instances :
-->

```php
Rocketeer::addTaskListeners(['deploy', new Rocketeer\Tasks\Setup], 'some-event', function ($task) {
  echo $task->releasesManager->getCurrentRelease();
});
```

## 作成したタスク内のイベント発火

<!--original
## Firing events in your own tasks
-->

あなたが作成したタスクでも`fireEvent`メソッドを使って、同じようにイベントを発火することができます。

<!--original
You can fire events in your own tasks too by using the `fireEvent` method :
-->

```php
class MyTask extends Rocketeer\Abstracts\AbstractTask
{
	public function execute()
	{
		$this->fireEvent('making-coffee');
		$this->makeCoffee();

		$this->fireEvent('drinking-coffee');
		$this->drinkCoffee();
	}
}
```

あなたのイベントに名前空間を必要としません。Rocketeerがそれをしてくれます。すべてのイベントは最初に`rocketeer.`名前空間されます。そしてカレントタスクを加え、上の2つのイベントは、`rocketeer.my-task.making-coffee`と`rocketeer.my-task.drinking-coffee`として発生します。

<!--original
You don't need to namespace your events, as Rocketeer will do it for you. It will first namespace all events in the `rocketeer.` space, then add a slug of the current task, so the two events above would be fired as `rocketeer.my-task.making-coffee` and `rocketeer.my-task.drinking-coffee`:
-->

```php
Rocketeer::listenTo('my-task.drinking-coffee', 'ls');
```

さて、手動で名前空間する必要がありますが、クロージャータスクの中でイベントを発火することもできます。すべてのクロージャータスクはその中心の無名関数にあり、無名関数でもありながら、すべてのイベントは`rocketeer.closure`の中で発火されます。

<!--original
Now, you can also fire events in Closure Tasks, by you will need to manually namespace those : as all Closure Tasks are at their core anonymous functions, they're anonymous tasks as well which means all events will get fired in `rocketeer.closure` :
-->

```php
Rocketeer::after('deploy', function ($task) {
	$task->fireEvent('some-event'); // Will get fired as rocketeer.closure.some-event
});
```

もし多数のクロージャータスクがある場合も、_それ自体_明確ではないにしろ、問題にはなりません。

<!--original
This is not a problem _per se_ but can get problematic if you have a lot of Closure Tasks.
-->

### 特定の順序でのイベント発火

<!--original
### Firing events in a particular order
-->

イベントシステムは`illluminate/events`を使うことで、自身の優先的なメソッドを継承しています。これは、イベントにリスナーが追加された全てのメソッドで、これらのリスナーの優先度を細かく指定できることを意味します。以下は単純な例です。

<!--original
As the events system use `illuminate/events`, it inherits its priority methods. That means that for every method that adds listeners to an event, you can specify a priority for these listeners. Simple example :
-->

```php
Rocketeer::after('deploy', function ($task) {
	$task->campfire->notify('New version deployed on the server');
});

Rocketeer::after('deploy', ['npm install', 'grunt']);
```

さて、理想を言えば、NPMパッケージがインストールされてGruntが流れを実行する時だけ、そこでエラーが起こるはずなので、キャンプファイヤーのチャットルームにデプロイについて注意喚起されるようにしたいとします。そのためには、優先度を呼び出しの最後に追加します。優先度は普通の整数で、もっとも低い優先度のものが最後に発火し、逆もまた同様です。私たちのキャンプファイヤー注意喚起が本当に全てリスナーの最後の最後に送られることを確実にするには、ただ以下のようにします。

<!--original
Now ideally you'd want your chat room on Campfire to be notified about the deployment only when the NPM packages are installed and Grunt has run its course, because an error might happen there. For this you add a priority at the end of the call : priority is a basic integer, listeners with lowest priority will be fired at the end, and vice versa. So to make sure our Campfire notification would get sent at really the very end of all our listeners, we can just do this :
-->

```php
Rocketeer::after('deploy', function ($task) {
	$task->campfire->notify('New version deployed on the server');
}, -10);

Rocketeer::after('deploy', function ($task) {
	$task->runForCurrentRelease(['npm install', 'grunt']);
});
```

これらは、引数に優先度を許容するいくつかのメソッドです。

<!--original
Here are some methods that accept a priority argument :
-->

```php
Rocketeer::before($task, $listeners, $priority = 0)
Rocketeer::after($task, $listeners, $priority = 0)
Rocketeer::listenTo($event, $listeners, $priority = 0)
Rocketeer::addTaskListeners($tasks, $event, $listeners, $priority = 0)
```

### イベント内でのキューの停止

<!--original
### Halting the queue in an event
-->

イベントで正確な`false`を返却することで、いつでもRocketeerはそれを認識しキュー全体を停止します。これは決められたメジャーなイベントの前でチェックしてキャンセルするのに有用です。例えば、デプロイで、いくつかの条件がそろわないかどうかなどです。

<!--original
Whenever an event returns a strict `false`, Rocketeer will recognize it and halt the whole queue. This is useful to do checks before certain major events and cancel per example deployment if some conditions are not met.
-->

キューをキャンセルするには、単にfalseを返します。

<!--original
To halt the queue you can either simply return false :
-->

```php
Rocketeer::before('deploy', function ($task) {
	if (!$something) {
		return false;
	}
});
```

もしくは、詳細情報を追加で渡したい場合には、どんなものでも渡したものをエラーとして表示させ、_同時に_falseを返すことができる`Task::halt`メソッドが使えます。

<!--original
Or if you want to pass additional details, you can use the `Task::halt` method which will display as error whatever you pass to it, and _then_ return false :
-->

```php
Rocketeer::before('deploy', function ($task) {
	if (!$something) {
		return $this->halt('Something was wrong here, cancelling');
	}
});
```

何を使ったとしても、Rocketeerは、キューがキャンセルされた旨と何のタスクかという追加のエラーメッセージを表示します。

<!--original
Whatever you use, Rocketeer will display an additional error message stating the queue was canceled and by what Task.
-->

## 利用可能なイベント

<!--original
## Available events
-->

全てのタスクは、デフォルトで`before`と`after`のイベントを持っていますので、すべてのストラテジーが実行されます。例として、`Deploy`タスクは`CreateRelease`、`Dependencies`、`Test`そして`Migrate`のストラテジーを一つ一つ呼び出します。
これは、デプロイの場合に、以下のイベントに自分自身でフックできることを意味します。

<!--original
All tasks have, by default, a `before` and `after` events, so do all strategies. Per example the `Deploy` task will respectively call the following strategies: `CreateRelease`, `Dependencies`, `Test` and `Migrate`.
That means you can, in the case of a deployment, hook yourself on the following events:
-->

```
deploy.before
create-release.before
create-release.after
dependencies.before
dependencies.after
test.before
test.after
migrate.before
migrate.after
deploy.before-symlink
deploy.after
```

リリースがカレントとしてシンボリックリンクされる前に発火される特殊なイベント`deploy.before-symlink`に注意してください。これは、リリースが有効にされる前に実行したい動作を配置するのに推奨されます。

<!--original
Notice the `deploy.before-symlink` event which is a special event fired before the release gets symlinked as current. This is the recommended place to do any work on the release before it goes live.
-->

### 失敗イベント

<!--original
### Failure events
-->

また、すべてのタスクは、紐づけられたイベントまたはサブタスクを失敗したとき、自分自身から`halt`イベントを発火します。

<!--original
All tasks also fire an `halt` event when they fail, be it from themselves, a bound event or a subtask. You can hook into those events like any other event.
-->

例として、もし、データベースバックアップシステムをもっていて、マイグレーションのデプロイに失敗したときは、データベースにバックアップからリストアしたいでしょう。でしたら以下のようにします。

<!--original
Per example if you have a database backup system and when deploying your migrations fail, you'd want to restore that backup of the databse, so you'd do the following:
-->

```php
Rocketeer::listenTo('migrate.halt', function() {
  // Restore the database
});
```
