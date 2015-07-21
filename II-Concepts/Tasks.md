# Tasks

An important concept in Rocketeer are Tasks. Most of the commands you see right above are using predefined Tasks underneath, like **Rocketeer\Tasks\Setup** or **Rocketeer\Tasks\Deploy**. A lot of those are already defined by Rocketeer but you can add your own tasks to Rocketeer to execute custom actions on your server too.

A task can be one of a three things:

- A simple one-line command which will be executed in the latest release's folder. Like `composer install` or an array of one-line commands.
- A closure, giving you access to Rocketeer's core helpers which allow you to perform more advanced actions.
- And finally a class, extending the `Rocketeer\Abstracts\AbstractTask` class, giving you full at-home control. All custom-made Tasks must have at least an `execute` method. And that's all.

Each level gives you a little more control and comfort – _this is intentional_. If you need more control than what Closures give you then you probably need a class.

-----

## Hooking into Rocketeer's Tasks

What most users will do is to hook into existing Rocketeer's Tasks. Rocketeer provides a built-in event system that allows you – at minimum – to execute actions before or after a Task's execution.

### Defining Tasks in the config file

You can hook into any task via the `hooks.php` file in Rocketeer's config file. The syntax is pretty straightforward. Below you can see an example of three types of Tasks mentioned above:

```php
<?php
'after' => array(
  'setup' => array(

    // Commands
    'composer install',

    // Actual Tasks classes
    'Rocketeer\Tasks\Cleanup',
    'MyNamespace\MyTaskClass',

    // Closures
    function($task) {
      $tests = $task->runForCurrentRelease('phpunit --coverage-html=tests/coverage');

      if ($tests) {
        $task->command->info('Tests ran perfectly dude !');
      } else {
        $task->command->error('Aw man, tests failed and stuff');
      }
    },

  ),
?>
```

### Defining Tasks using the facade

Rocketeer also provides you with a facade you can use. It's especially useful if you don't want to put stuff in the config file, as it can get dirty when using closures.

Rocketeer allows two things: creating a `.rocketeer/events.php` file where all your hooks are contained or if you have more files like classes and want one file per task, you can simply create a `.rocketeer/events/` folder and every task you put in it will automatically be loaded by Rocketeer.

```php
<?php
use Rocketeer\Facades\Rocketeer;

Rocketeer::before('deploy', function($task) {
  $task->command->info('Sup guys');
});

Rocketeer::after(['update', 'deploy'], array(
  'composer install',
  'bower install'
));

Rocketeer::after('deploy', 'MyClass');
?>
```

As a first argument you give the name of a Task (or an array of names) you'd like to act on and then your task. Again, you can use the three types of tasks: strings, closures or classes.

## Defining your own Tasks

Rocketeer also provides you with a task-running system to create, manage and run your own tasks. You can put those either in a `.rocketeer/tasks.php` file or - if you have a lot of them - in a `./rocketeer/tasks/` folder which will load all the files in it.

### Via the facade

```php
<?php
Rocketeer::task('composer', 'composer install');

Rocketeer::task('composer', array(
  'composer self-update',
  'composer install',
));

Rocketeer::task('phpunit', function ($task) {
  return $task->runForCurrentRelease('phpunit');
}, 'Runs the PHPUnit tests');
?>
```

The `task` method takes three arguments: name of the task, its execution (one-line command, closure, class) and a description of what it does.

These tasks will be automatically registered with Rocketeer. You'll then be able to execute them either via the CLI by doing for example `php rocketeer composer` or via the facade:

```php
Rocketeer::execute('composer');

Rocketeer::execute(['composer', 'phpunit']);
```

### Via classes

```php
<?php
namespace MyTasks;

class Migrate extends \Rocketeer\Abstracts\AbstractTask
{
  /**
   * Description of the Task
   *
   * @var string
   */
  protected $description = 'Migrates the database';

  /**
   * Executes the Task
   *
   * @return void
   */
  public function execute()
  {
    $this->explainer->line('Running migrations');

    return $this->runForCurrentRelease('php artisan migrate --seed');
  }
}
?>
```

Classes aren't automatically registered with Rocketeer so you'll need to do that manually. You can either do so via the config file in the `tasks.custom` array:

```php
'custom' => array(
  'MyTasks\Migrate',
),
```

or via the facade:

```php
Rocketeer::add('MyTasks\Migrate');
```

And there you go, tadah!

![artisan](http://i.imgur.com/jwdQ2Ly.png)

## Executing tasks

Once a task is defined, it will be available in two places: the command line interface and the facade.

If you registered a task named `composer`, e.g. you'll be able to do this:

```php
$ php rocketeer composer
```

Or in your PHP code (in `tasks.php`):

```php
Rocketeer::execute('composer');
```

You can execute multiple tasks by passing an array of tasks. One thing that is crucial to remember is that Rocketeer will always process the queue you pass to it. That means you can pass anything that is considered as a task:
- A string command.
- A closure.
- The name of a task.
- The class of a task.

```php
<?php
Rocketeer::execute(array(
  'my-task',

  'composer install',

  'Rocketeer\Tasks\Deploy',

  function ($task) {
    return $task->run('ls');
  },
));
?>
```

### Executing tasks in local

In some cases you need to execute a series of commands in local and have the same comfort as you'd have executing things on the local server. This is only possible with actual Tasks classes. All you need to do is to set the `local` property to *true*:

```php
class MyTask extends Rocketeer\Abstracts\AbstractTask
{
  protected $local = true;
}
```

From there, Rocketeer will delegate all calls to a `LocalConnection` class that acts the same as any Connection class but runs commands in the local system. This is useful for preparing the deploy and is how the `Primer` task works.

-----

## Writing Tasks

### Core methods

The core method of any Task is the `run` method. This is the one that lays at the bottom of nearly every other helper.
It just runs commands on the remote server and returns the output.

```php
<?php
$folders = $this->run('ls');
?>
```

You can also pass to it an array of commands to execute. Now, note this because it's important: every call to `run` is self contained. Meaning this (`pwd` returns the current folder):

```php
<?php
// Returns /
$this->run('cd first-folder');
$folder = $this->run('pwd');

// Returns /first-folder/
$folder = $this->run(array(
  'cd first-folder',
  'pwd',
));
?>
```

To automate running of tasks in folders two helpers exist: `runInFolder` and `runForCurrentRelease`. The first one will run one or more tasks in a folder, while the other will run one or more tasks in the current release's folder.

```php
<?php
$this->run(array(
  'cd /home/www/website/releases/123456789',
  'ls',
));

// Is the same as

$this->runInFolder('releases/123456789', 'ls');

// Is the same as

$this->runForCurrentRelease('ls');
?>
```

### Folder helpers

A few folder/file manipulation methods are also present. They're very basic and just abstracting low-level bash commands but, hey, they're good to have:

```php
<?php
$this->move('folder/file.php', 'new-folder/file.php');

$array = $this->listContents('folder');

$boolean = $this->fileExists('file.php');
$boolean = $this->fileExists('folder');

$this->createFolder('folder');
$this->removeFolder('folder');

$this->symlink('folder-a', 'folder-b');

$phpunit = $this->which('phpunit', 'vendor/bin/phpunit'); // Second argument is fallback
?>
```

### Tasks-related methods

Some methods are used by other Rocketeer tasks and can be utilized to create your own ones. All of them are relative to the current release.

```php
<?php
// Run tests
$boolean = $this->runTests();
$boolean = $this->runTests('--stop-on-failure');

// Run migrations
$this->runMigrations();
$this->runMigrations(true); // Seeds the database too

// Run Composer
$this->runComposer();

// Set folders as web-writable
$this->setPermissions('public/images/users');
?>
```

### External methods

Tasks also have access to the other classes of Rocketeer. You can call other tasks:

```php
<?php
$this->executeTask('Rollback');
```

And call other classes's methods. All tasks and strategies have access to the following core classes and their methods:

- **command** is the instance of the command currently running, to fetch options and arguments;
- **scm** is the binary instance of the current SCM (git, SVN, etc.);
- **rocketeer** is responsible for getting information on the current app and its configuration;
- **connections** handles getting the current connection/stage/server and its respective credentials;
- **remote** is your entry point to the server, it's the class the `run` method uses;
- **explainer** is responsible for displaying the output, you'll use it to display e.g. progress in your tasks;
- **paths** finds local and remote paths;
- **releasesManager** handles releases and their paths;
- **localStorage** class keeps the local storage file up to date, it tracks the remote server's state and credentials;
- **builder** allows you to build tasks and strategies on the fly;
- **queue** allows you to run tasks in a queue within your tasks;
- **tasks** handles registration of tasks and their events, it's the class behind the Rocketeer facade.

You can find a list of the methods available for each of these services directly in the [API documentation](http://rocketeer.autopergamene.eu/api/namespaces/Rocketeer.html).
They are accessed as a property, e.g. to get a folder in the current release you would do the following:

```php
$folder = $task->releasesManager->getCurrentReleasePath('some/folder');
```
