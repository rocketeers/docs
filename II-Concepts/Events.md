# Events

Rocketeer is at its core driven by Tasks. Each of these tasks has a whole ecosystem of events fired in their lifetime, powered by the [illuminate/events](https://github.com/illuminate/events) components. Therefor, when you're doing this :

```php
Rocketeer::before('deploy', 'MyApp\MyCustomTask');
```

What you're actually doing is, more simply put :

```php
$app['events']->listen('rocketeer.deploy.before', 'MyApp\MyCustomTask@execute');
```

This is not "just this" of course, as Rocketeer does some magic on the second argument so that `Rocketeer::before('deploy', 'composer install')` transforms `composer install` into an actual Task class the Events Dispatcher can call. But in its concept, it's just your basic _Dispatcher_ (or _Observer_ if you like) system.

## Registering events

First of all, you can register events in any file that is autoloaded by Composer. If you're using the Rocketeer archive and have no particular autoloading, Rocketeer will by default try to load `.rocketeer/events.php` or if you have more events and want to split them in multiple files, it'll autoload every file in a `.rocketeer/events/*` folder.

## Listening to events

All tasks in Rocketeer fire two basic events : `before` and `after` on which you can hook. But some tasks fire internal events, during their execution, allowing you to execute actions at various points in their lifetime. To listen to these events, there are two methods you can use. Say you want to execute something before the **Deploy** task symlinks the `current` folder to the latest release :

```php
Rocketeer::addTaskListeners('deploy', 'before-symlink', function ($task) {
  echo $task->releasesManager->getCurrentRelease();
});

// Or

Rocketeer::listenTo('deploy.before-symlink', function ($task) {
  echo $task->releasesManager->getCurrentRelease();
});
```

These two methods look very similar but in appearance only, the first one actually calls the second but allows you to pass an array of tasks names or instances :

```php
Rocketeer::addTaskListeners(['deploy', new Rocketeer\Tasks\Setup], 'some-event', function ($task) {
  echo $task->releasesManager->getCurrentRelease();
});
```

## Firing events in your own tasks

You can fire events in your own tasks too by using the `fireEvent` method :

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

You don't need to namespace your events, as Rocketeer will do it for you. It will first namespace all events in the `rocketeer.` space, then add a slug of the current task, so the two events above would be fired as `rocketeer.my-task.making-coffee` and `rocketeer.my-task.drinking-coffee`:

```php
Rocketeer::listenTo('my-task.drinking-coffee', 'ls');
```

Now, you can also fire events in Closure Tasks, by you will need to manually namespace those : as all Closure Tasks are at their core anonymous functions, they're anonymous tasks as well which means all events will get fired in `rocketeer.closure` :

```php
Rocketeer::after('deploy', function ($task) {
	$task->fireEvent('some-event'); // Will get fired as rocketeer.closure.some-event
});
```

This is not a problem _per se_ but can get problematic if you have a lot of Closure Tasks.

### Firing events in a particular order

As the events system use `illuminate/events`, it inherits its priority methods. That means that for every method that adds listeners to an event, you can specify a priority for these listeners. Simple example :

```php
Rocketeer::after('deploy', function ($task) {
	$task->campfire->notify('New version deployed on the server');
});

Rocketeer::after('deploy', ['npm install', 'grunt']);
```

Now ideally you'd want your chat room on Campfire to be notified about the deployment only when the NPM packages are installed and Grunt has run its course, because an error might happen there. For this you add a priority at the end of the call : priority is a basic integer, listeners with lowest priority will be fired at the end, and vice versa. So to make sure our Campfire notification would get sent at really the very end of all our listeners, we can just do this :

```php
Rocketeer::after('deploy', function ($task) {
	$task->campfire->notify('New version deployed on the server');
}, -10);

Rocketeer::after('deploy', function ($task) {
	$task->runForCurrentRelease(['npm install', 'grunt']);
});
```

Here are some methods that accept a priority argument :

```php
Rocketeer::before($task, $listeners, $priority = 0)
Rocketeer::after($task, $listeners, $priority = 0)
Rocketeer::listenTo($event, $listeners, $priority = 0)
Rocketeer::addTaskListeners($tasks, $event, $listeners, $priority = 0)
```

### Halting the queue in an event

Whenever an event returns a strict `false`, Rocketeer will recognize it and halt the whole queue. This is useful to do checks before certain major events and cancel per example deployment if some conditions are not met.

To halt the queue you can either simply return false :

```php
Rocketeer::before('deploy', function ($task) {
	if (!$something) {
		return false;
	}
});
```

Or if you want to pass additional details, you can use the `Task::halt` method which will display as error whatever you pass to it, and _then_ return false :

```php
Rocketeer::before('deploy', function ($task) {
	if (!$something) {
		return $this->halt('Something was wrong here, cancelling');
	}
});
```

Whatever you use, Rocketeer will display an additional error message stating the queue was canceled and by what Task.

## Available events

All tasks have, by default, a `before` and `after` events, so do all strategies. Per example the `Deploy` task will respectively call the following strategies: `CreateRelease`, `Dependencies`, `Test` and `Migrate`.
That means you can, in the case of a deployment, hook yourself on the following events:

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

Notice the `deploy.before-symlink` event which is a special event fired before the release gets symlinked as current. This is the recommended place to do any work on the release before it goes live.