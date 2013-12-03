# Tasks

An important concept in Rocketeer is Tasks : most of the commands you see right above are using predefined Tasks underneath : **Rocketeer\Tasks\Setup**, **Rocketeer\Tasks\Deploy**, etc.
Now, the core of Rocketeer is you can hook into any of those Tasks to perform additional actions, for this you'll use the `before` and `after` arrays of Rocketeer's config file.
You can also add Tasks to Rocketeer to use directly via Rocketeer itself by doing `php rocketeer mytask` per example.

A task can be three things :

- A simple one-line command which will be executed in the latest release's folder, like `composer install`
- A closure, giving you access to Rocketeer's core helpers to perform more advanced actions
- And finally a class, extending the `Rocketeer\Traits\Task` class, giving you full at-home control. All custom-made Tasks must have at least an `execute` method. And that's all.

Each level gives you a little more control and comfort – _this is intentional_, if you need more control than what Closures give you, then you probably need a class.

-----

## Hooking into Rocketeer's Tasks

What most user will do is hook into the existing Rocketeer's Tasks to do things before, or after. There is two ways to do that :

### Defining Tasks in the config file

You can hook into any task via the `tasks` array in Rocketeer's config file. The syntax is pretty basic, here you can see an example with the three types of Tasks mentionned above :

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
        $task->command->error('Aw man, tests failed and stuff')
      }
    },
  ),
?>
```

### Defining Tasks using the facade

Rocketeer also provides you with a facade to use, if you don't want to put stuff in the config file, as it can get dirty with closures. I recommend you put those hooks in your `app/start/artisan.php` file if you're in Laravel, otherwise you can create a `tasks.php` file where your `rocketeer.php` file is.

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

You give as first argument the name of the name of the Task you'd like to act on (or an array of names), and then your task. Again, you can use the three types of tasks : strings, closures or classes.

## Defining Tasks classes

Sometimes you have things to do that don't fit in with the existing Tasks Rocketeer provides. That's why you can create your own tasks, here is an example one. As you can see it's pretty easy.

```php
<?php
class Migrate extends Rocketeer\Traits\Task
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
    $this->command->info('Running migrations');
    $this->runForCurrentRelease('php artisan migrate --seed');
  }
}
?>
```

### Registering your custom Task

Now that the class is created, you need to register it with Rocketeer. As with the hooks, you can either do that via the config file, in the `tasks.custom` array :

```php
'custom' => array(
  'Migrate',
),
```

Or via the facade in a custom file as seen above :

```php
Rocketeer::add('Migrate');
```

And there you go, tadah !

![artisan](http://i.imgur.com/jwdQ2Ly.png)

-----

## Writing Tasks

### Core methods

The core methods of any Task is the `run` method, this is the one that lies at the bottom of nearly every other helper.
It just runs commands on the remote server, and returns the output.

```php
<?php
$folders = $this->run('ls');
?>
```

You can also pass it an array of commands to execute. Now, note this because it's important : every call to `run` is self contained. Meaning this (`pwd` returns the current folder) :

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

To automate running tasks in folders, two helpers exist : `runInFolder` and `runForCurrentRelease`. The first one will run one or more tasks in a folder, while the other one will run one or more tasks in the current release's folder.

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

A few folder/file-manipulation methods are also present, they're very basic and just abstract low-level bash commands but, hey, they're good to have :

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

Some methods are used by other Rocketeer tasks and can be used by you to create your own. All of them are relative to the current release.

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

// Set folders as web-writtable
$this->setPermissions('public/images/users');
?>
```

### External methods

Tasks also have access to the other classes of Rocketeer. You can call other tasks :

```php
<?php
$this->executeTask('Rollback');
```

And call other classes's methods. There are five main classes surrounding Tasks which you'll like want to use :
- The **Command** is the command executing the Task. You'll use it to display messages and fetch options and arguments
- The **Releases Manager** handles releases and their paths
- The **Server** class keeps up to date the `deployments.json` file which tracks the remote server's state
- The **Remote** is your entry point to the server, it's the class the `run` method uses
- The **Rocketeer** classes handles informations provided by the User (config and stuff)

```php
<?php
$this->releasesManager->getCurrentRelease();
$this->releasesManager->getPathToRelease('123456789');
$this->releasesManager->getPreviousRelease();
$this->releasesManager->getReleases();

$this->server->setValue('key', 'value');
$this->server->getValue('key');
$this->server->getRepository();

$this->rocketeer->getHomeFolder();
$this->rocketeer->getApplicationName();
$this->rocketeer->getRepository();
$this->rocketeer->getOption('remote.shared');

$this->command->argument('argument');
$this->command->option('verbose');
$this->command->info('It works !');
$this->command->error('It does not work !');

$this->remote->run(array(
  'cd folder', 'ls',
));
?>
```

This is not all of it of course, you can check out these classes's sources for a full documented list of the methods available.