# Tasks

An important concept in Rocketeer is Tasks : most of the commands you see right above are using predefined Tasks underneath : **Rocketeer\Tasks\Setup**, **Rocketeer\Tasks\Deploy**, etc.
Now, the core of Rocketeer is you can hook into any of those Tasks to peform additional actions, for this you'll use the `before` and `after` arrays of Rocketeer's config file.
You can also add Tasks to Rocketeer to use directly via Artisan.

A task can be three things :
- A simple one-line command, like `composer install`
- A closure, giving you access to Rocketeer's core helpers to perform more advanced actions
- And finally a class, extending the `Rocketeer\Tasks\Task` class, giving you full at-home control. All custom-made Tasks must have at least an `execute` method. And that's all.

Each level gives you a little more control and comfort – this is intentional, if you need more control than what Closures give you, then you probably need a class.

## Hooking into Rocketeer's Tasks

What most user will do is hook into the existing Rocketeer's Tasks to do things before or after. There is two ways to do that :

### Defining Tasks in the config file

You can hook into any task via the `tasks` array in Rocketeer's config file. The syntax is pretty basic, here you can see an example with the three types of Tasks mentionned above :

```php
<?php
'after' => array(
  'setup' => array(

    // Commands
    'composer install',

    // Actual Tasks classes
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
```

### Defining Tasks using the facade

Rocketeer also provides you with a facade to use, if you don't want to put stuff in the config file :

```php
<?php
Rocketeer::before('deploy', function($task) {
  $task->command->info('Sup guys');
});

Rocketeer::after('deploy', array(
  'composer install',
  'bower install'
));

Rocketeer::after('deploy', 'MyClass');
```

You give as first argument the name of the name of the Task you'd like to act on, and then your task.

## Creating your own Tasks

Sometimes you have things to do that don't fit in with the existing Tasks Rocketeer provides. That's why you can create your own tasks, here is an example one. As you can see it's pretty easy.

```php
<?php
class Migrate extends Rocketeer\Tasks\Task
{

  /**
   * Description of the Task
   *
   * @var string
   */
  public $description = 'Migrates the database';

  /**
   * Executes the Task
   *
   * @return void
   */
  public function execute()
  {
    $this->command->info('Running migrations');
    $this->runMigrations();
  }
}
```

### Registering your custom Task

 Now that the class is created, you need to register it with Rocketeer. As with the hooks, you can either do that via the config file, in the `tasks.custom` array :

```php
'custom' => array(
  'Migrate',
),
```

Or via the facade in your `app/start/artisan.php` file :

```php
Rocketeer::add('Migrate');
```

And there you go, tadah !

![artisan](http://i.imgur.com/jwdQ2Ly.png)

-----

To learn about what you can do with Tasks, check out the [API](API)