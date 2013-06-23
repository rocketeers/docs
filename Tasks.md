## Tasks

An important concept in Rocketeer is Tasks : most of the commands you see right above are using predefined Tasks underneath : **Rocketeer\Tasks\Setup**, **Rocketeer\Tasks\Deploy**, etc.
Now, the core of Rocketeer is you can hook into any of those Tasks to peform additional actions, for this you'll use the `before` and `after` arrays of Rocketeer's config file.

A task can be three things :
- A simple one-line command, like `composer install`
- A closure, giving you access to Rocketeer's core helpers to perform more advanced actions
- And finally a class, extending the `Rocketeer\Tasks\Task` class, giving you full at-home control

Each level gives you a little more control and comfort – this is intentional, if you need more control than what Closures give you, then you probably need a class.

So the three kind of tasks above could be seen in your config file :

```php
<?php
'after' => array(
  'deploy:setup' => array(

    // Commands
    'composer install',

    // Closures
    function($task) {
      $task->rocketeer->goTo('releases/134781354');
      $tests = $task->run('phpunit');

      if ($tests) {
        $task->command->info('Tests ran perfectly dude !');
      } else {
        $task->command->error('Aw man, tests failed and stuff')
      }
    },

    // Actual Tasks classes
    'MyNamespace\MyTaskClass',
  ),
```

```php
<?php
namespace MyNamespace;

class MyTaskClass extends Rocketeer\Tasks\Task
{
  public function execute()
  {
    $currentReleasePath = $this->releasesManager->getCurrentReleasePath();
    $this->rocketeer->goTo($currentReleasePath;
    $tests = $this->run('phpunit');

    if ($tests) {
      $this->command->info('Tests ran perfectly dude !');
    } else {
      $this->command->error('Aw man, tests failed and stuff')
    }
  }
}
```
