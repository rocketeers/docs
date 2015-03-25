[translation here]

<!--original
# Services
-->

[translation here]

<!--original
When creating tasks, strategies, binaries, etc., Rocketeer provides you with a variety of services you can access and use to ease your work. While all of them are describe in the public API, some are more important than others. This is a look at what Rocketeer provides you with, and gives you a little more insight on how things work under the hood.
-->

[translation here]

<!--original
All examples provided are from the viewpoint of a custom task. But it also applies to closure tasks, meaning if within a task class you can do `$this->foo->bar()`, within a closure you can do this:
-->

```php
Rocketeer::before('deploy', function (AbstractTask $task) {
  $task->foo->bar();
});
```

[translation here]

<!--original
The string between brackets next to each service is the handle it can be access through.
-->

[translation here]

<!--original
## Tasks Builder [builder]
-->

[translation here]

<!--original
The tasks builder is one of Rocketeer's most important services, its a powerful factory that allows you to quickly get instances of binaries, tasks, strategies and so on, from within your tasks. It can build tasks from anything that is allowed: one-liners, closures, instances, or class names.
-->

```php
// Build a task
$this->builder->buildTask('ls');

$this->builder->buildTask('Acme\MyCustomTask');

$this->builder->buildTask(function ($task) {
  return $task->run('ls');
}, 'My task', 'Lists the folders');

// Build multiple tasks
$this->builder->buildTasks(array(
  'ls',
  'Acme\MyCustomTask',
));

// Build the configured implementation of a strategy
$this->builder->buildStrategy('Deploy');

// Build a particular implementation of a strategy
$this->builder->buildStrategy('Deploy', 'Clone');

// Build the Command instance related to a Task
$this->builder->buildCommand('Deploy'); // DeployCommand
```

[translation here]

<!--original
## Tasks Queue [queue]
-->

[translation here]

<!--original
The tasks queue is the other major class of Rocketeer, it receives an array of tasks, and builds a runnable pipeline from it. Anything it receives is passed through the TasksBuilder so you can pass the same kind of tasks types (closures, strings, class names, etc.) as above.
-->

```php
// Run an array of tasks
$this->queue->run(['cd /foo/bar', 'ls']);

// Run an array of tasks and return the last line
// You can specify the connection as second argument
$files = $this->queue->execute(['cd /foo/bar', 'ls'], 'production');

// Same as above but with reversed arguments
$files = $this->queue->on('production', ['cd /foo/bar', 'ls']);
```

[translation here]

<!--original
The `run` method will return an instance of `Rocketeer\Services\Tasks\Pipeline`. The Pipeline is a class extending `Illuminate\Support\Collection` that stores instances of `Rocketeer\Services\Tasks\Job`.
-->

[translation here]

<!--original
Within Rocketeer, a Job is a bundled version of a queue, containing all the necessary information required to run it: what connection the queue needs to be run on, what stage, what server, etc. Imagine you have two connections, `production` and `staging`, you'll get the following pipeline:
-->

```php
$pipeline = $this->queue->run(['cd /foo/bar', 'ls']);

$firstJob = $pipeline->first(); // Job instance
$firstJob->connection; // production
$firstJob->queue; // [ClosureTask(cd /foo/Bar), ClosureTask(ls)]

$secondJob = $pipeline->get(1); // Job instance
$firstJob->connection; // staging
$firstJob->queue; // [ClosureTask(cd /foo/Bar), ClosureTask(ls)]
```

[translation here]

<!--original
Once the pipeline is built it is either run synchronously or asynchronously depending on whether the `--pretend` flag was passed.
-->

[translation here]

<!--original
## Tasks Handler [tasks]
-->

[translation here]

<!--original
The tasks handler registers the tasks, plugins, and events that surrounds them. It's the main task behind the `Rocketeer` facade so you should be familiar with most of its methods (before, after, listenTo, task):
-->

```php
// Register tasks and events
$this->tasks->task('list-files', 'ls', 'Lists files in a folder');

$this->tasks->before('Deploy', 'list-files');

// Get bound events
$events = $this->tasks->getTasksListeners('deploy', 'before');
```

[translation here]

<!--original
## Steps Builder [steps()]
-->

[translation here]

<!--original
The steps builder is what allows tasks to run a series of internal commands and calls and halts on the first failure, while keeping a fluent interface. Take the following series of commands inside an imaginary task:
-->

```php
$this->executeTask('Migrate');
$this->symlink('foo', 'bar');
```

[translation here]

<!--original
Now ideally we would want to not execute the symlink if the Migrate task fails, for this, we simply prepend those calls with `steps()`:
-->

```php
$this->steps()->executeTask('Migrate');
$this->steps()->symlink('foo', 'bar');
```

[translation here]

<!--original
This will store those "steps" within a `steps` property on the tasks. Once this is done we can safely run them sequentially until one fails:
-->

```php
$this->steps()->executeTask('Migrate');
$this->steps()->symlink('foo', 'bar');

// Run the steps until one fails
if (!$this->runSteps()) {
	return $this->halt();
}
```

[translation here]

<!--original
## Queue Explainer [explainer]
-->

[translation here]

<!--original
The queue explainer is what drives Rocketeer's CLI output, its job is to explain to the user what is happening, what tasks are fired by what task or event, what is their progress, their result, how long they took to execute, etc.
-->

[translation here]

<!--original
By default when executing a task, the explainer will automatically display information about it from the `name` and `description` property you set on it (or passed to `Rocketeer::task`). But you can provide additional details to the explainer during the flow of your task:
-->

```php
$this->explainer->line('Doing some stuff');
if (!$this->somethingThatMayFail()) {
  $this->explainer->error('Something crashed here');
}

$this->explainer->success('All went well!');
```

[translation here]

<!--original
The explainer passes that to the Command instance's `line` method, meaning you can add foreground and background colors as described in [Symfony's documentation](http://symfony.com/doc/current/components/console/introduction.html#coloring-the-output):
-->

```php
$this->explainer->line('<info>Something</info> tried and <bg=red>failed</bg=red>');
```

[translation here]

<!--original
As you may have noticed, the explainer builds a "tree" of your tasks at runtime, nesting tasks if they are fired within a task, or an event:
-->

```
|-- Running: Deploy (Deploys the website) [~14.25s]
|---- Running: Primer (Run local checks to ensure deploy can proceed)
|---- Running: CreateRelease (Creates a new release on the server) [~5.98s]
|------ Running strategy for Deploy: Sync
```

[translation here]

<!--original
You can do this to by wrapping some commands in a closure:
-->

```php
$this->explainer->displayBelow(function() {
  // Whatever is done here will be displayed one level below in the tree
});
```
