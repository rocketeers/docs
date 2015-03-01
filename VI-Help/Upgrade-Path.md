# アップグレードパス

<!--original
# Upgrade path
-->

以前のバージョンのRocketeerのアップグレードする場合に、確認しておくべき主要な変更事項は以下の通りです。

<!--original
If you're upgrading from a previous version of Rocketeer, here are the major changes you'll need to look for:
-->

## 1.2から2.0.0への変更

<!--original
## From 1.2 to 2.0.0
-->

- Projectは、5.4+ベースです。まだ5.3を使っている場合、5.3は[終息しています。](http://php.net/archive/2014.php#id2014-08-14-1)アップグレードしましょう。
- `Rocketeer\Traits\Task` は `Rocketeer\Abstracts\AbstractTask` に移動しました。
- `Rocketeer\Traits\Plugin` は `Rocketeer\Abstracts\AbstractPlugin` に移動しました。
- `Rocketeer\Plugins\Notifier` は `Rocketeer\Plugins\AbstractNotifier` にフォルダ名が変更になりました。
- `$task->server` プロパティは `$task->localStorage` に名称が変更になりました。
- すべての接続/リポジトリ関連のメソッドは 以前の`$task->rocketeer` から `$task->connections` へと変更になりました。(`getConnection`, `getStage`, `getConnectionCredentials`, など)
- `getConnectionCredentials` は、1つのサーバーであっても、常に資格情報を配列で返すようになりました。単体のサーバーまたは、特定の1台のサーバーの資格情報を取得するには、`$task->connections->getServerCredentials(connection = current, server = 0)` を呼び出してください。
- 次のメソッド `php`, `artisan`, `runArtisan`, `runMigrations`, `runSeed`, `runTests`, `composer` , `runComposer` は、削除されて、バイナリーシステムへ置き換えられました(関連ドキュメントを参照してください)。たとえば、以前の`$task->runMigrations(seed = false)`は、`$task->artisan()->migrate(seed = false)`となります。
- 次のメソッド `cloneRepository`, `copyRepository`, `updateRepository` は削除され、ストラテジーシステムへ置き換えられました(関連ドキュメントを参照してください)。以前の`$task->updateRepository()`は、`$task->getStrategy('Deploy')->update()`となります。
- 以下のイベントは、非推奨となりサブタスクやそれぞれに適したbefore/afterのフックに置き換えられました。
	- `cloneRepository` および `copyRepository` => `create-release.before` へ
	- `runComposer` => `dependencies.before` へ
	- `checkTestsResults` => `test.before` へ

<!--original
- Project is now 5.4+, if you still use 5.3, which is [now EOL](http://php.net/archive/2014.php#id2014-08-14-1), it's time to upgrade
- `Rocketeer\Traits\Task` was moved to `Rocketeer\Abstracts\AbstractTask`
- `Rocketeer\Traits\Plugin` was moved to `Rocketeer\Abstracts\AbstractPlugin`
- `Rocketeer\Plugins\Notifier` was renamed to `Rocketeer\Plugins\AbstractNotifier`
- The `$task->server` property was renamed to `$task->localStorage`
- Every connection/repository related method that was previously on `$task->rocketeer` was moved to `$task->connections` (`getConnection`, `getStage`, `getConnectionCredentials`, etc)
- The `getConnectionCredentials` now always return an array of server credentials even there is only one server. To get the credentials of the only server or of one in particular, call `$task->connections->getServerCredentials(connection = current, server = 0)`
- The following methods: `php`, `artisan`, `runArtisan`, `runMigrations`, `runSeed`, `runTests`, `composer` and `runComposer` have been removed and replaced by the Binaries system (see relevant documentation). Per example if you were doing `$task->runMigrations(seed = false)` you'll now do `$task->artisan()->migrate(seed = false)`
- The following methods: `cloneRepository`, `copyRepository` and `updateRepository` have been removed and replaced by the Strategy system (see relevant documentation). If you were doing `$task->updateRepository()` you'll now do `$task->getStrategy('Deploy')->update()`
- The following events are deprecated and have been replaced by subtasks and their matching before/after hooks:
	- `cloneRepository` and `copyRepository` => `create-release.before`
	- `runComposer` => `dependencies.before`
	- `checkTestsResults` => `test.before`-->
