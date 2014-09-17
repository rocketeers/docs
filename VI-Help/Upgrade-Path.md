# Upgrade path

If you're upgrading from a previous version of Rocketeer, here are the major changes you'll need to look for:

## From 1.2 to 2.0.0

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
	- `checkTestsResults` => `test.before`